# Environment notes

The upstream repo has no `requirements.txt`; the notebooks install packages
inline with `pip install` cells. The labs target Colab, which preinstalls most
of what is listed below.

## Branches

- `main`: upstream notebooks untouched, for running on Colab (VS Code Colab
  extension). The pip cells are intact.
- `local-uv`: a local `uv` project (Python 3.12, pinned because TensorFlow
  supports 3.9-3.12) with ruff, ty and the VS Code settings. The pip cells that
  were active upstream are commented out there, since packages come from
  `pyproject.toml` instead.

## Hardware (local laptop)

Intel Raptor Lake-P/U iGPU ("Intel UHD Graphics"), no CUDA.

- On `local-uv`, `pyproject.toml` pulls `torch` and `triton-xpu` from the
  PyTorch XPU index. Verify with `uv run python scripts/check-xpu.py`.
- The XPU build exposes `torch.xpu`, **not** `torch.cuda`. Check with
  `torch.xpu.is_available()`; `torch.cuda.is_available()` is always `False`.
- PyTorch warns that the GPU is older than Arc (Alchemist): best-effort support only.
- TensorFlow has no XPU path on Windows, so it runs on CPU.
- Only lab05 (transformers fine-tune) and lab07 (sentence-transformers fine-tune)
  are heavy enough for a GPU to matter. Use Colab for those.

## `pip install` cells (as found upstream)

"Upstream" is the state on `main`. Cells marked "active" are commented out on
`local-uv`; cells marked "commented out" were already disabled upstream.

| Notebook | Command | Upstream |
|---|---|---|
| `lab01/introduction.ipynb` | `%pip install numpy` | active |
| `lab01/introduction.ipynb` | `%pip install pandas` | active |
| `lab02/lab2.ipynb` | `%pip install pygad` | commented out |
| `lab03/lab3.ipynb` | `%pip install scikit-learn` | commented out |
| `lab04/FeatureSelection.ipynb` | `pip install datasets` | commented out |
| `lab04/FeatureSelection.ipynb` | `pip install sklearn_relief` | commented out |
| `lab05/DeepLearningLabIIS.ipynb` | `pip install datasets` | active |
| `lab05/DeepLearningLabIIS.ipynb` | `pip install transformers[torch]` | active |
| `lab05/DeepLearningLabIIS.ipynb` | `pip install accelerate -U` (x4, duplicated) | active |
| `lab07/sentence_transformers_finetuning_demo.ipynb` | `pip install -U sentence-transformers datasets` | active |
| `lab09/AutoBOT_example.ipynb` | `pip install git+https://github.com/skblaz/autobot.git` | active |
| `lab10/ReinforcementLearning.ipynb` | `pip install gymnasium` | commented out |
| `lab10/ReinforcementLearning.ipynb` | `pip install pygame` | commented out |
| `lab10/ReinforcementLearning.ipynb` | `pip install gymnasium[box2d]` | commented out |
| `lab10/ReinforcementLearning.ipynb` | `pip install tqdm` | active |

## Imported third-party packages

Found by grepping `import`/`from` lines in every `.ipynb`. Standard-library
modules are omitted. Lab10 may import more than the cells found; not fully audited.

| PyPI package (import name) | Labs |
|---|---|
| numpy | 01-06, 08-10 |
| pandas | 01-06, 08, 09 |
| matplotlib | 02-05, 08, 09 |
| scipy | 02, 03, 08 |
| scikit-learn (`sklearn`) | 03, 04, 05, 06, 08, 09 |
| seaborn | 05, 08, 09 |
| ipython (`IPython.display`) | 05 (already a kernel dependency) |
| tqdm | 05, 10 |
| datasets | 04, 05, 06, 07 |
| transformers | 05, 06, 09 |
| evaluate | 05, 06 |
| accelerate | 05, 06 (needed by the HF `Trainer`) |
| torch | 07 (also pulled in by transformers) |
| tensorflow (incl. `keras`) | 05, 06, 09 |
| nltk | 06 |
| gensim | 06 |
| stanza | 06 |
| plotly | 06 |
| sentence-transformers (`sentence_transformers`) | 07 |
| xgboost | 08, 09 |
| pygad | 02, 09 |
| sklearn_relief | 04 |
| autoBOTLib | 09 (git install, see above) |
| openml | 09 |
| tpot | 09 |
| autogluon (`autogluon.tabular`) | 09 |
| tabpfn | 09 |
| hyperopt | 09 |
| shap | 09 |
| interpret | 09 |
| gymnasium, pygame | 10 |

Not installed by any pip cell upstream (they rely on Colab): matplotlib, scipy,
seaborn, plotly, nltk, gensim, stanza, evaluate, xgboost, openml, tpot,
autogluon, tabpfn, hyperopt, shap, interpret, tensorflow.

## Known risks (local install)

- `sklearn_relief` is unmaintained and may not install on current Python.
- `autogluon`, `tpot`, `tensorflow` and `torch` pin overlapping dependencies and
  often conflict. Plan to use separate `uv` dependency groups (e.g. `lab09`,
  `tf`) so one conflict does not block everything.
- `autoBOTLib` needs `uv add git+https://github.com/skblaz/autobot.git`.

## Plan

- Local (`local-uv`): labs 01-04 and 08 (numpy, pandas, matplotlib, scipy,
  scikit-learn, seaborn, tqdm).
- Colab (`main`): labs 05, 06, 07, 09, plus the pip cells for packages Colab
  lacks.
