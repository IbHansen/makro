# MAKRO in ModelFlow

MAKRO is the Danish macroeconomic model made by DREAM, written in GAMS. This book shows
its release 2026-September (<https://github.com/DREAM-DK/MAKRO>) translated to ModelFlow.

- All 1476 GAMS equations of the simulation model are translated. After expansion over the
  sets, the ModelFlow model has 20 474 equations.
- Evaluated on DREAM's baseline, every equation holds for 2030-2129, the terminal year
  included.
- MAKRO looks forward, so the whole horizon 2030-2129 is solved in one stacked Newton: 100
  years with 15 472 simultaneous variables each, 1.5 million unknowns together.
- DREAM's eleven published standard shocks are reproduced: the largest difference in the
  effect on the headline variables is 6e-8.

## The chapters

- **[A simple experiment](makro26_experiment_simple.ipynb)** raises public purchases by 1 per
  cent from 2030, using only standard ModelFlow: load the model, run the baseline, update,
  solve, and look at the results.
- **[All standard shocks](makro26_all_shocks.ipynb)** solves DREAM's eleven standard shocks,
  checks each solution against DREAM's own, and saves them all in one file.
- **[Report on the shocks](makro26_shocks_report.ipynb)** reads that file and shows tables and
  charts of the shocks by groups of variables. Nothing is solved.
- **[The input-output system](makro26_io_viz.ipynb)** shows where each sector's production goes
  and the import content of demand in the baseline.
- **[A shock in the input-output system](makro26_io_shock.ipynb)** shows how one shock changes
  the deliveries, the import content and the prices.

In the last four chapters the code is folded. Click a cell's bar to see it.

## Loading the model and the solutions

Two files hold everything the chapters need. Both load with one call:

```python
from modelclass import model

# the model and DREAM's baseline
mmakro, baseline = model.modelload(r'data\makro26_baseline.pcimz')

# the same model with the baseline and all eleven shocks as kept solutions
mshocks, _ = model.modelload(r'data\makro26_shocks.pcimz')
list(mshocks.keep_solutions)
# ['Baseline', 'Labour productivity', 'Labour supply', ...]
```

| File | Holds | Size | Load time |
| --- | --- | --- | --- |
| `makro26_baseline.pcimz` | the model, the baseline, descriptions, derivative expressions, solve options | 20 MB | 1.1 seconds |
| `makro26_shocks.pcimz` | the same, plus 12 kept solutions (`keep_solutions`): the baseline and the eleven shocks | 261 MB | 5.5 seconds |

The files were saved from a model loaded with `straight=True`: the equations stay in the order
of the file and the analysis of the simultaneous structure is skipped. With that analysis the
load takes about two minutes. The stacked Newton does not use it.

Each kept solution is a whole dataframe, 117 years by 39 034 columns. ModelFlow's own tools work
on them: `mshocks.keep_plot('QBNP', diffpct=True)` draws GDP for every shock as the per cent
difference from the baseline.

## Solving all eleven shocks

The shocks are permanent and unfinanced, from 2030, solved for 2030-2129. Each is solved in two
steps as DREAM does: 1 per cent of the shock scaled up, then the whole shock.

| Shock | Minutes | Largest equation error | Largest difference from DREAM |
| --- | --- | --- | --- |
| Labour productivity | 1.5 | 7.2e-10 | 4.4e-05 |
| Labour supply | 0.5 | 6.4e-08 | 3.4e-05 |
| Export market | 0.4 | 2.6e-09 | 4.4e-05 |
| Import prices | 0.4 | 1.1e-07 | 4.0e-05 |
| Capital productivity | 0.4 | 1.4e-08 | 4.7e-05 |
| Public employment | 0.6 | 2.3e-06 | 4.4e-05 |
| Public purchases | 0.5 | 4.3e-07 | 4.7e-05 |
| Transfers to households | 0.5 | 3.3e-09 | 4.0e-05 |
| Interest rate | 0.7 | 2.5e-07 | 4.5e-05 |
| Foreign prices | 0.4 | 2.6e-08 | 3.7e-05 |
| Public investment | 1.4 | 4.2e-08 | 3.9e-05 |
| **All eleven** | **7.7** | | |

The shocks are in the order they were solved. Public investment is solved last: it is the only
one that needs the derivatives recalculated at its own starting point. The difference from DREAM
is over all 20 474 variables and all years, relative to the size of the variable.

The notebooks were run on a desktop computer. The book shows the stored results; the
pages don't run anything. Solving the model needs about 25 GB of memory, which is too much
for a browser.

## Films of the population

MAKRO's baseline holds a population projection: the population by one-year age groups and
the survival rate by age, for every year to 2129. Three films show it year by year. They are
made by the notebooks `makro26_population_film.ipynb` and `makro26_life_expectancy_film.ipynb`
from the baseline file alone. MAKRO has no split by sex and only net migration, so the films
show both sexes together.

:::{figure} film/makro26_population_by_age.mp4
The age distribution, 2016-2129. One bar per age; the thin black line is the first year.
:::

:::{figure} film/makro26_births_deaths_migration.mp4
Births, deaths and net migration, with net migration by age to the right. The jumps in net
migration in 2022-2026 are in MAKRO's data, not something the model calculates.
:::

:::{figure} film/makro26_life_expectancy.mp4
Survival and life expectancy, calculated from MAKRO's survival rates. The blue curve is the
share still alive at each age with the rates of the year; the grey bars are the distribution of
the age at death.
:::
