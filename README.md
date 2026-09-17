# rew-bounded-benchmarking

Postprocessing writes three LaTeX tables to `data/rewbnd-latex`:

- `tablebenchmarks.tex`: benchmark sizes, reward bounds, and epoch counts.
- `tableunf.tex`: unfold results with cutoff and discretization statistics.
- `tablet1800.tex`: ra-unfold versus ra-bel-seq, using the best configurations
  within 1800 seconds. All four lines of each selected result cell are
  highlighted with `RWTHblue25`, mixed with gray on shaded rows, matching the
  POMDP benchmarking repository.
  Highlights select the best lower and upper bounds, breaking ties by runtime.
  The reward-aware unfolded state count is shown under the ra-unfold header.

In both result tables, each cut/discr cell contains the result, runtime,
selected size threshold or resolution, and belief MDP state count, in that
order.
When no result is available, the cell reports `TO` (timeout), `MO` (memory
out), or `ERR` from the underlying runs, combining labels as `TO / MO` when
both occurred. Failure cells also span four lines. Cells without run data
are blank, matching the POMDP benchmarking repository.

The tables use `array`, `booktabs`, `multirow`, and `xcolor` with its `table`
option, along with the document macros `\model`, `\config`, `\epochs`,
`\tupleaccess`, `\vecrew`, `\sizethreshold`, and `\resolution`.
Main-set table labels use `-b1`, `-b2`, and so on only when the same model label
would otherwise occur more than once, for example `serv-b1`, `water-b2`, and
`resrc-b1`. The benchmark table lists the corresponding concrete bounds.

All generated plot TeX files are in `data/rew-bounded-plots`, with one plot per
`<collection>-<instance>.tex` file (for example, `bnds_result-clean.tex`).
Time-plot filenames include the full benchmark identifier and parameters, such
as `time_result-main_serv_rbrmax1_B10570.tex`, so variants remain distinguishable.
Each contains an explicit `tikzpicture`/`axis` environment with embedded
coordinates and local colors, based on the original plot formulation.
Individual plots need no custom plot commands, document macros, or external
CSV files. The CSV exports remain available in `data`.
The three collection files `time_result.tex`, `lvls_result.tex`, and
`bnds_result.tex` contain only
`\input` statements for the individual files, using paths relative to the
repository root.

For a shared legend, each full plot has a matching legend-free file in
`data/rew-bounded-plots/nolegend`, named `<plot>-nolegend.tex`. Each collection also
has a compact `<collection>-legend.tex` file. This avoids relying on
`legend to name`, whose legend state does not survive TikZ externalization.
For example, the level and reward-bound plots can be placed in subfigures with
`nolegend/lvls_result-clean-nolegend.tex` and
`nolegend/bnds_result-clean-nolegend.tex`, followed by
`lvls_result-legend.tex`. Give all three inputs distinct
`\tikzsetnextfilename` names when externalizing them.
Legend-free plots use `width=\linewidth`, so they occupy the largest possible
width inside a `0.49\linewidth` subfigure and two fit side by side.
Time plots use a logarithmic time axis. Level plots use a logarithmic axis
from 1 to 120; reward-bound plots use a linear axis from 50 to 102. Both use
value bounds from 0.82 to 1.008 and symbolic threshold/level labels, with local
macro fallbacks for standalone use. `\costthresh` aliases the dissertation's
`\boundtuple` (bold t). Cutoff and discretization curves share each method's color and
marker, with one legend entry per method that has data.
Legend labels use `\config`, with a local small-caps fallback when the parent
document does not define it.

To include an individual plot (or use a collection filename to include them all):

```tex
\documentclass{article}
\usepackage{pgfplots}
\pgfplotsset{compat=1.18}
\begin{document}
\input{data/rew-bounded-plots/bnds_result-clean.tex}
\end{document}
```

Regenerate the exports from the existing experiment logs with:

```sh
python3 scripts/postprocess.py logs_diss_rew logs_diss_rew_fo logs_diss_rew_lvls logs_diss_rew_bnds
```

`test-results.tex` is a minimal preview of all three tables and all three plot
collections. It includes the required macros, tuple brackets, and fonts from
the dissertation and uses its B5 layout settings. It does not load files from
the dissertation directory. Compile from this repository's root with:

```sh
lualatex --interaction=nonstopmode --halt-on-error --output-directory=data test-results.tex
```

The PDF is written to `data/test-results.pdf`, with each plot's filename above
it for identification. It also includes a non-externalized example of two
subfigures using one generated legend. Comment out individual `\input` lines
in the test document to preview only selected tables or plot collections.
