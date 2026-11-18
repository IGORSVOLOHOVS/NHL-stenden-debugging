# SPEC: nhl-stenden-debugging

A software requirements specification reverse-engineered on 2026-11-18 from
commit `4483888` (2026-11-08). It describes the behaviour the code shows; it
records no intent beyond what the files state. The notebook path `debugging_solution.ipynb` is
shortened to `nb` below, and `nb:N` is line N of the notebook's JSON file.

## 1. Purpose and scope

The repository holds a solved debugging assignment (`README.md:3-5`): four Python
exercises with a planted bug each, the fixed code in a Jupyter notebook, and
written answers (`ANSWERS.md`).

- **In scope:** the four fixed functions and classes in `nb`; their demo cells;
  the answer documents and images.
- **Out of scope:** the original buggy code, which is not in the repository; any
  installable package or service (`recipe.ps1:16-17`); the shared lab tooling,
  except FR-19.

## 2. Context

| Item | Evidence |
|---|---|
| Actor | A reader or grader of the assignment, and whoever re-runs the notebook. Who they are is not stated (see section 9). |
| Runtime | A Jupyter kernel displayed as `tue_tasks` (kernel name `python3`) on Python 3.10.20 (`nb:1858-1860`, `nb:1872`). The notebook carries nbgrader cell metadata and the "Create Assignment" toolbar (`nb:1856`). |
| External system | The MNIST download through `torchvision.datasets.MNIST`, then an `ossci-datasets` S3 mirror as fallback (`nb:727-740`). |
| Hardware | A CUDA device if one is available, else the CPU (`nb:690`). `ANSWERS.md:57-66` lists the PC the work ran on (RTX 3060 Ti GPU, Windows 10). |
| Working directory | `data_file.csv` (`nb:445`) and `MNIST/` (`nb:727`) are written here. |

## 3. Functional requirements

### Exercise 1: fruit by index

- **FR-1.** The system shall return the element at position `fruit_id` of the list `fruits`.
  Source: `nb:107`, `nb:131`. Verification: no test.
- **FR-2.** The system shall raise `RuntimeError("Fruit with id <id> does not exist")` when `fruit_id < 0` or `fruit_id >= len(fruits)`.
  Source: `nb:129-130`. Verification: no test.

### Exercise 2: swap bounding-box coordinates

- **FR-3.** The system shall return an array in which columns 0 and 1, and columns 2 and 3, are exchanged, with column 4 (class id) unchanged.
  Source: `nb:272`. Verification: no test.
- **FR-4.** The system shall leave the input array unmodified and work on a copy.
  Source: `nb:271`. Verification: no test.

### Exercise 3: precision-recall plot

- **FR-5.** The system shall read the CSV file with a comma delimiter, skip the first row, and skip rows that are empty or have fewer than two fields.
  Source: `nb:400-404`. Verification: no test.
- **FR-6.** The system shall convert the first two fields of each row to `float`.
  Source: `nb:406`. Verification: no test.
- **FR-7.** The system shall plot column 0 on the x axis labelled "Precision" and column 1 on the y axis labelled "Recall", limit both axes to [-0.05, 1.05], and call `plt.show()`.
  Source: `nb:410-415`. Verification: no test.
- **FR-8.** The demo cell shall write `data_file.csv`, with header `precision,recall` and 11 data rows, to the working directory before it calls `plot_data`.
  Source: `nb:445-460`. Verification: no test.

### Exercise 4: GAN on MNIST

- **FR-9.** The generator shall map a 100-value latent vector through linear layers 100-256-512-1024-784, with ReLU between them and Tanh at the end, and reshape the result to (N, 1, 28, 28).
  Source: `nb:515-536`. Verification: no test.
- **FR-10.** The discriminator shall flatten its input to 784 values and pass it through linear layers 784-1024-512-256-1. Each hidden layer shall have ReLU and Dropout(0.3), and the output shall be a Sigmoid.
  Source: `nb:592-615`. Verification: no test.
- **FR-11.** `train_gan` shall load the MNIST training set into root `"."` with `download=True`, `ToTensor` and `Normalize((0.5,), (0.5,))`, and shall batch it with a shuffling `DataLoader` of size `batch_size`.
  Source: `nb:724-727`, `nb:742`. Verification: no test.
- **FR-12.** If the first load raises any exception, `train_gan` shall print "Failed to download MNIST, retrying with different URL", replace `MNIST.resources` with the `ossci-datasets` URLs and checksums, and load again.
  Source: `nb:728-740`. Verification: no test.
- **FR-13.** Before training, `train_gan` shall display a 4x4 grid of 16 real images titled "Real images" and then wait 5 seconds.
  Source: `nb:745-757`. Verification: no test.
- **FR-14.** `train_gan` shall train both networks with `BCELoss` and their own `Adam` optimiser at learning rate 0.0001.
  Source: `nb:760-765`. Verification: no test.
- **FR-15.** `train_gan` shall size the real labels, fake labels and latent batches from `real_samples.size(0)`, so that a final, smaller batch gives matching tensor sizes.
  Source: `nb:771-777`, `nb:791`. Verification: no test.
- **FR-16.** When training the discriminator, `train_gan` shall concatenate the real samples with detached generated samples.
  Source: `nb:779-788`. Verification: no test.
- **FR-17.** `train_gan` shall train the generator on newly drawn latent samples, with the loss computed against the "real" labels.
  Source: `nb:790-797`. Verification: no test.
- **FR-18.** On the last batch of each epoch (`n == len(train_loader) - 1`), `train_gan` shall clear the cell output and display a 4x4 grid of 16 generated images. The title shall give the epoch and both losses to two decimals.
  Source: `nb:799-811`. Verification: no test.

### Repository standard

- **FR-19.** The repository shall carry the repo-quality-template tooling at revision 4.0.0. Its `recipe.ps1` shall act on `test`, `info` and `clean`, and shall print `nothing to <verb>` for every other verb.
  Source: `README.md:34`, `recipe.ps1:40-73`. Verification: `.\recipe.ps1 info` and `.\recipe.ps1 run` (run 2026-11-18, both exit 0).

### Declared but not implemented

- `nb:652` and `nb:701` say that the code "produces the expected results at first attempt at about 50 epochs". No check confirms this, and the notebook does not define the expected result. The saved run of cell 23 shows the grid of every epoch from 0 to 99.
- The docstring examples (`>>>`, for example `nb:381-396` and `nb:720`) give no expected output, and no doctest runner is configured.
- `ANSWERS.md:10` proposes `SortedSet` from `sortedcontainers` as an alternative fix. The code uses a list.
- `README.md:13` says `debugging_solution.pdf` is "the same notebook printed". The PDF has no text layer, so this could not be checked.
- `README.md:18-19` says exercise 3 does not run from a fresh clone. FR-8 contradicts it.

## 4. Interfaces

| Interface | Signature or form | Inputs | Output and side effects | Source |
|---|---|---|---|---|
| `id_to_fruit` | `(fruit_id: int, fruits: List[str]) -> str` | an index and a list | the element; `RuntimeError` if out of range | `nb:107-131` |
| `swap` | `(coords: np.ndarray)` | an [n, 5] array | a new array with x and y swapped | `nb:240-273` |
| `plot_data` | `(csv_file_path: str)` | a CSV path | a matplotlib plot | `nb:362-415` |
| `Generator.forward` | `(x)` | a (N, 100) tensor | a (N, 1, 28, 28) tensor | `nb:533-536` |
| `Discriminator.forward` | `(x)` | a tensor with 784 values per sample, for example (N, 1, 28, 28) | a (N, 1) tensor of probabilities | `nb:612-615` |
| `train_gan` | `(batch_size=32, num_epochs=100, device=...)` | see FR-11 to FR-18 | figures in the notebook; downloads to `./MNIST` | `nb:690-811` |
| `recipe.ps1` | `.\recipe.ps1 <verb>` | `build install uninstall clean run publish info test` | see FR-19 | `recipe.ps1:1-7` |

## 5. Data

| Entity | Format | Storage | Source |
|---|---|---|---|
| Fruit list | `List[str]` | in memory | `nb:150-152` |
| Bounding boxes | integer array `[x1, y1, x2, y2, classid]` per row | in memory | `nb:244-250`, `nb:293-297` |
| Precision-recall data | CSV: header row, then two float columns | `./data_file.csv`, not tracked and not ignored | `nb:445-459` |
| MNIST training set | torchvision dataset; 28x28 grey images normalised with mean 0.5 and std 0.5 | `./MNIST/`, ignored by `.gitignore:28` | `nb:724-727` |
| Saved outputs | PNG figures embedded in the notebook; the training cell holds 102 figures | `debugging_solution.ipynb` | `nb` (cell 23 outputs) |
| Answer images | 6 PNG files | repository root, in Git LFS | `ANSWERS.md:44-54`, `.gitattributes:9` |

## 6. Non-functional requirements

- **NFR-1.** The notebook targets Python 3.10, the kernel it was saved with. Source: `nb:1872`.
- **NFR-2.** Training shall run on CUDA when available and on the CPU otherwise. Source: `nb:690`, `nb:706`.
- **NFR-3.** The first run of exercise 4 needs network access. Source: `nb:727`.
- **NFR-4.** PDFs and PNGs are stored in Git LFS, so reading them requires Git LFS. Source: `.gitattributes:8-9`.
- **NFR-5.** No key is committed: `.claude/.env` is ignored. Source: `.gitignore:24`, `CLAUDE.md` rule 4.
- **NFR-6.** The content is under the MIT licence. Source: `LICENSE`.

## 7. Constraints and dependencies

- Imports: `csv`, `numpy`, `typing`, `torch`, `torchvision`, `matplotlib.pyplot`,
  `IPython.display`, `PIL`, `time` (`nb:19-35`). No requirements file or lock
  file pins their versions. The saved figures name matplotlib 3.10.8 in their
  PNG metadata; the other versions are not recorded.
- Tooling pin: ruff `==0.16.1` (`.claude/ruff.toml:2`). The message of commit
  `5ca926d` says that pytest is pinned too, but `.claude/pytest.ini` contains no
  version constraint.

## 8. Acceptance

1. In a Jupyter kernel with the packages from section 7, run all cells in order.
   Cell 4 gives `orange`, `kiwi` and `strawberry` (FR-1). Cell 9 maps the row
   `[10, 5, 15, 6, 0]` to `[5, 10, 6, 15, 0]` and leaves `coords` unchanged
   (FR-3, FR-4). Cell 13 creates `data_file.csv` and plots precision on x (FR-5
   to FR-8). Cell 23 shows one figure per epoch without a `ValueError`
   (FR-11 to FR-18).
2. Run `train_gan(batch_size=64, num_epochs=1)`. It should not raise the size
   mismatch quoted at `nb:669` (FR-15).
3. `.\recipe.ps1 test` exits 0 (FR-19).

On 2026-11-18, step 1 was checked for FR-1 to FR-8 only: the cell sources were
run outside Jupyter with numpy 2.3.3 and a stub in place of matplotlib. Steps 2
and 3 and the rest of step 1 were not run.

## 9. Open questions

- Which course the assignment belongs to, and who reads or grades it. The README
  names only NHL Stenden, and the kernel display name `tue_tasks` is not explained. The
  year is answered: `README.md:3` says March 2026, and the commit that added
  the notebook, `03cbc40`, is dated 2026-03-30.
- The original buggy code is not kept, so the fixes cannot be compared with it.
  `ANSWERS.md` describes the bugs in words only.
- The torch, torchvision and numpy versions the notebook ran with. The
  matplotlib version is answered: 3.10.8, named in the PNG metadata of all 103
  saved figures.
- Whether the fixed `train_gan` was ever run with `batch_size=64`.
- Answered: the epochs the six images in `ANSWERS.md` come from. Their titles
  read epochs 27, 37, 47, 65, 87 and 99 (`image.png`, then `image copy.png` to
  `image copy 5.png`). `image.png` and `image copy 5.png` match the saved
  figures of epochs 27 and 99 in cell 23; the other four were not compared.
- Whether a Gitea runner serves this repository, so that the `.gitea/workflows`
  gates actually run.
- Whether `ANSWERS.pdf` should be regenerated to match `ANSWERS.md` after
  `9748277`.
- What `debugging_py/` (`.gitignore:27`) held. It has been ignored since
  `03cbc40`, the commit that added the notebook, and was never tracked.
