# nhl-stenden-debugging

A solved debugging assignment from March 2026, named for NHL Stenden: four
exercises of Python code with a planted bug each, the fixed code in a notebook,
and written answers. It is a document, not a program.

## What is here

- `debugging_solution.ipynb` holds the four exercises and their fixes. Exercise 1
  picks a fruit by index from a set, exercise 2 swaps bounding-box coordinates in
  a NumPy array, exercise 3 plots a precision-recall curve from a CSV file, and
  exercise 4 trains a small GAN, a generator and a discriminator, in PyTorch.
  `debugging_solution.pdf` is the same notebook printed.
- `ANSWERS.md` answers the two questions of each exercise, shows six images of
  the GAN's output and lists the machine it ran on. `ANSWERS.pdf` is the same text
  printed, and the six `image*.png` files are the images it shows.

Exercise 3 reads `data_file.csv`, which is not in this repository, so that cell
does not run from a fresh clone. The notebook was not run here.

## Working on it

This repository ships no program. `.\recipe.ps1` is still its whole interface:
`.\recipe.ps1 test` runs pytest and ruff over `.claude`, and `.\recipe.ps1 info`
prints the metadata. The other verbs print `nothing to <verb>`. `CLAUDE.md`
holds the rules.

## License

MIT, as it stood on GitHub. See [LICENSE](LICENSE).
