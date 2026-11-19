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

**[A simple experiment](makro26_experiment_simple.ipynb)** raises public purchases by 1 per
cent from 2030, using only standard ModelFlow: load the model, run the baseline, update,
solve, and look at the results.

The notebook was run on a desktop computer. The book shows the stored results; the
pages don't run anything. Solving the model needs about 25 GB of memory, which is too much
for a browser.
