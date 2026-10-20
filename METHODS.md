# Methods (v1.0.0)

## 1. Source files

Each test column is built from one or more real consumer raw-data files, downloaded on the dates given in `data/datasets.csv`. Files are grouped by test version, and *n* is the number of files for that test. Notes on specific files:

- **23andMe v5** (*n* = 2): the October 2026 file is v5. The two v5 files differ slightly (434 IDs appear in only one of them).
- **AncestryDNA v2 (2020 download)** (*n* = 1): a kit from 2017, downloaded in 2020.
- **AncestryDNA (Mar 2026)** (*n* = 2): files on the current chip. Ancestry changed its chip and shortened raw-data files around March 2026.
- **MyHeritage (2026)** (*n* = 2): files from two different people, downloaded March and June 2026. Both have identical ID lists.
- **StrateGene (testing era, 2022)** (*n* = 1): a file from when StrateGene offered its own testing. It now offers reports from other companies' raw data.
- **SelfDecode** is excluded from these files. Its downloads leave out inconclusive calls, so its ID lists vary from person to person. SelfDecode results appear in the live tool only.

## 2. Presence extraction

- Only the **first column** (the variant ID) of each raw-data file was read. Genotype, chromosome, and position columns were never copied or stored.
- Comment and header lines were skipped.
- IDs were lowercased and de-duplicated within each file.
- **`GSA-` prefix:** an ID written as `GSA-rsNNNN` was normalised to `rsNNNN`. This mainly affected tellmeGen. No Genetic Lifehacks variant depends on this step for its tellmeGen result.
- No other aliasing was applied. Merged or retired rsIDs, and 23andMe `i` IDs, are matched exactly as written.
- A test's ID is "seen" if it appears in at least one of that test's files. *k* is the number of the test's files that contain it.

## 3. What's in the release

- **All-IDs file:** every rsID and 23andMe `i` ID seen in at least one of the nine tests (1,680,374 rsIDs + 63,188 `i` IDs = 1,743,562 rows).
- **Left out of the all-IDs file:** chip-specific probe names that aren't rsIDs or `i` IDs (for example, `chr…`, `ilmnseq_…`, or `cftr_c.…` names; 49,088 IDs). These names differ between chips and some include variant notation, so they were left out of v1.0.0. IDs seen only in SelfDecode files were also dropped.
- **Genetic Lifehacks file:** each variant ID from the Genetic Lifehacks article tables (2,171 distinct IDs: 2,108 rsIDs and 63 23andMe `i` IDs). Each comes with the article(s) that discuss it and its presence in each test.
- **Article summary:** for each of the 413 articles, each test's coverage of the article's distinct variant IDs.

## 4. Evidence rules

| Situation | Verdict |
|---|---|
| *k* ≥ 1 | `seen`: Seen in real file |
| *k* = 0, *n* ≥ 2 | `not_in_files`: Not in files |
| *k* = 0, *n* = 1 | `unknown_single_file`: Not seen in the 1 file — unknown |
| *k* = 0, ID is a 23andMe `i` ID | `unknown_non_rs_id`: Unknown (the variant may be present under an rsID) |

**Why file presence stands in for chip content:** 23andMe, AncestryDNA, MyHeritage, and FamilyTreeDNA keep a row for every tested position, even when the call fails (shown as `--` or `0 0`). So a row missing from these files usually means the chip didn't include that variant. Even so, absence is never treated as definitive with only one file.

On the article summary and in the live tool's article view, a variant that wasn't seen counts as **not covered** by that test's files.

## 5. Known limitations

- **Individual files vary.** A person's file can still lack a covered SNP because of no-calls (often about 0.5–3% of positions), or because the company removes low-confidence calls from the download.
- **Version drift.** Companies revise chips and download contents without notice. The results apply to the files and dates listed.
- **Small *n*.** Six of the nine tests have a single file.
- **ID naming.** The same variant can appear under different IDs on different chips (merged rsIDs, `i` IDs, probe names). Matching is exact, so some "not covered" results may be naming differences.
- **Not seen in any test.** 18 Genetic Lifehacks variants weren't seen in any of the nine tests (for example rs61748511, rs121912436, i5039448). Most are rare or clinical variants that need sequencing.
- **No imputation.** Imputed genotypes aren't considered.
- **Not exhaustive.** Other tests (for example Living DNA and whole-genome sequencing) aren't included in v1.0.0.

## 6. Privacy

The release contains variant IDs, presence counts, verdicts, and Genetic Lifehacks article titles and URLs only. It has no genotypes, alleles, reference or effect alleles, positions, file hashes, or per-person source lists. Before release, an automated check confirmed that the files contain no allele-like columns or values and that no sampled genotype rows from the source files appear in them.
