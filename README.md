# Genetic Lifehacks SNP Coverage Dataset

**Which consumer DNA tests include each SNP?**

This dataset records which SNPs (by rsID) appear in real raw-data files from nine consumer DNA test versions: 23andMe v3, v4, and v5, two AncestryDNA chips, MyHeritage, tellmeGen, FamilyTreeDNA, and StrateGene (from when it offered testing). It also shows how well each test covers the 2,171 variants discussed in 413 [Genetic Lifehacks](https://www.geneticlifehacks.com) articles.

You can search it in the browser with the **SNP Coverage Checker**: <https://www.geneticlifehacks.com/snp-coverage/>

- Version: 1.0.0 (released 2026-10-20). Files last checked October 2026.
- License: [CC BY 4.0](LICENSE), covering the data and the documentation.
- DOI: [10.5281/zenodo.XXXXXXX](https://doi.org/10.5281/zenodo.XXXXXXX)

## What's in this repository

| File | Rows | What it is |
|---|---:|---|
| `data/snp-coverage-all-rsids-v1.0.0.csv.gz` | 1,743,562 | Every rsID (and 23andMe `i` ID) seen in at least one checked file, with per-test presence counts. Gzipped CSV. |
| `data/snp-coverage-genetic-lifehacks-v1.0.0.csv` | 2,171 | Variants used in Genetic Lifehacks articles: article title(s) and URL(s), plus presence counts and a verdict for each test. |
| `data/article-coverage-summary-v1.0.0.csv` | 413 | One row per Genetic Lifehacks article: how many of its variants each test covers, as a percentage, plus the IDs that aren't covered. |
| `data/datasets.csv` | 9 | One row per test column: display name, chip note, number of files (*n*), download dates, and notes. |
| `METHODS.md` | | How presence was determined, the evidence rules, and the limitations. |
| `CITATION.cff`, `.zenodo.json` | | Citation metadata for GitHub and Zenodo. |
| `CHANGELOG.md` | | Version history. |

All files are UTF-8 CSV with a header row. Multiple values in one cell are separated by ` | ` (articles) or by a space (ID lists).

## Data dictionary

### Test column prefixes

Each test has a machine-friendly prefix. Prefixes that would start with a digit get an `x` in front (`x23andme_…`), so they work as variable names in R and pandas.

| Prefix | Display label | Files (*n*) |
|---|---|---:|
| `x23andme_v3` | 23andMe v3 | 1 |
| `x23andme_v4` | 23andMe v4 | 1 |
| `x23andme_v5` | 23andMe v5 | 2 |
| `ancestrydna_v2_2020` | AncestryDNA v2 (2020 download) | 1 |
| `ancestrydna_2026` | AncestryDNA (Mar 2026) | 2 |
| `myheritage_2026` | MyHeritage (2026) | 2 |
| `tellmegen_2026` | tellmeGen | 1 |
| `familytreedna` | FamilyTreeDNA | 1 |
| `strategene_2022` | StrateGene (testing era, 2022) | 1 |

`data/datasets.csv` has more detail on each, including download dates, chip notes, and the number of IDs seen.

### Per-test columns (`<prefix>_…`)

| Column | Type | Meaning |
|---|---|---|
| `<prefix>_seen` | 0/1 | 1 if the ID was in at least one of that test's files. |
| `<prefix>_k` | integer | Number of that test's files that contain the ID. |
| `<prefix>_n` | integer | Number of that test's files checked (the same in every row). |
| `<prefix>_verdict` | code | GL file only. See the verdict codes below. |

### `snp-coverage-all-rsids-v1.0.0.csv.gz`

| Column | Meaning |
|---|---|
| `variant_id` | rsID (`rs…`) or 23andMe internal ID (`i…`), lowercase. Sorted with rsIDs first, by number. |
| `id_type` | `rs` or `i` (23andMe internal ID). |
| `in_genetic_lifehacks` | 1 if the ID appears in a Genetic Lifehacks article (see the GL file). |
| `seen_dataset_count` | Number of the 9 tests in which the ID was seen (1–9). |
| `<prefix>_seen`, `<prefix>_k`, `<prefix>_n` | As above, for each of the 9 tests. |

### `snp-coverage-genetic-lifehacks-v1.0.0.csv`

| Column | Meaning |
|---|---|
| `variant_id`, `id_type` | As above. |
| `article_count` | Number of Genetic Lifehacks articles that discuss the variant. |
| `article_titles` | Article title(s), separated by ` \| `. |
| `article_urls` | Article URL(s), separated by ` \| `, in the same order as the titles. |
| `seen_dataset_count` | Number of the 9 tests in which the ID was seen (0–9). |
| `<prefix>_seen`, `<prefix>_k`, `<prefix>_n`, `<prefix>_verdict` | As above, for each of the 9 tests. |

### `article-coverage-summary-v1.0.0.csv`

| Column | Meaning |
|---|---|
| `article_title`, `article_url` | The Genetic Lifehacks article. |
| `variant_count` | Number of distinct variant IDs in the article. |
| `<prefix>_covered` | How many of the article's variants were seen in that test's files. |
| `<prefix>_pct_covered` | `covered / variant_count × 100`, to one decimal place. |
| `<prefix>_not_covered_ids` | Space-separated IDs **not covered** by that test's files. |

### Verdict codes

| Code | Display text | Rule |
|---|---|---|
| `seen` | Seen in real file | Seen in ≥1 of the *n* files. |
| `not_in_files` | Not in files | Seen in 0 of *n* files, and *n* ≥ 2. |
| `unknown_single_file` | Not seen in the 1 file — unknown | Seen in 0 of 1 file. With a single file, absence isn't treated as evidence. |
| `unknown_non_rs_id` | Unknown | A 23andMe `i` ID that wasn't seen. Other companies may carry the same variant under an rsID. |

## Evidence rules in brief

- **Seen** means the ID was in at least one real raw-data file for that test.
- **Not in files** is used only when the ID was missing from all of at least two files for that test.
- With **only one file**, a missing ID is shown as **unknown**, not as "not on the chip."
- On the article summary, a variant that wasn't seen counts as **not covered** by that test's files.

`METHODS.md` has the full method.

## Limitations

- **Your own file can still be missing a SNP that's covered here.** Individual files have no-calls (often about 0.5–3% of positions), and companies change chips and download contents over time.
- A "not covered" or "not in files" result describes the files we checked on the dates we checked them. It doesn't prove that a company has never tested a variant, and it doesn't account for imputation.
- Several tests have only one file (*n* = 1), so for those tests absence is shown as unknown.
- Some variants are listed under different IDs on different chips (merged rsIDs, 23andMe `i` IDs, chip-specific probe names). This dataset matches IDs exactly, apart from the normalisation described in METHODS.md.
- 18 Genetic Lifehacks variants weren't seen in any of the nine tests. Most are rare or clinical variants that need sequencing.
- SelfDecode results appear only in the [live tool](https://www.geneticlifehacks.com/snp-coverage/), not in these files.
- This is educational information, not medical advice.

## Privacy

The dataset contains **presence only**: which variant IDs appear in a test's raw-data file. It has **no genotypes, alleles, positions, or per-person data** of any kind. It was built from the ID column of each file only, and no file-level identifiers are published. The source files were used with permission.

## Trademarks

23andMe, AncestryDNA, MyHeritage, tellmeGen, FamilyTreeDNA, and StrateGene are trademarks of their respective owners. The names appear here only to identify which test a file came from. Genetic Lifehacks is not affiliated with, endorsed by, or sponsored by any of these companies. No logos are used.

## How to cite

> Moon, D. (2026). *Genetic Lifehacks SNP Coverage Dataset: Which consumer DNA tests include each SNP* (Version 1.0.0) [Data set]. Zenodo. https://doi.org/10.5281/zenodo.XXXXXXX

GitHub's "Cite this repository" button (from `CITATION.cff`) gives APA and BibTeX versions.

## Corrections

If you spot an error, or have a raw-data file that disagrees with these results, please open a GitHub issue or use the contact form at <https://www.geneticlifehacks.com/say_hello/>. Corrections will go into the next version.
