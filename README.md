# District-level election information from Turkish Wikipedia

A Python research script for collecting district-level party information from Turkish Wikipedia's province election pages and exporting tables for longitudinal political research.

The script contains separate workflows for general and local elections. It is an independent extraction tool, not an official election-results release, and it does not collect a complete national vote-count dataset.

## Current coverage

The province lists in the script contain **10 provinces**: Adana, Adıyaman, Diyarbakır, Gaziantep, Hatay, Kahramanmaraş, Kilis, Malatya, Osmaniye and Şanlıurfa.

| Workflow | Configured years | Extracted information |
| --- | --- | --- |
| General elections | 1983, 1987, 1991, 1995, 1999, 2002, 2007, 2011, 2015, 2018 | Party listed in the district highest-vote table |
| Local elections | 1984, 1989, 1994, 1999, 2004, 2009, 2014, 2019 | Party with the highest parsed vote count in each selected table |

These lists define attempted requests, not guaranteed successful coverage. For years with multiple elections, such as 2015, the script does not explicitly distinguish election months.

## How it works

`WkikipediaScraper.py` (filename as stored in the repository):

1. Constructs province/year page URLs, including selected Turkish suffix exceptions.
2. Retrieves HTML with `requests` and parses tables with Beautiful Soup.
3. For general elections, targets the “İlçelerde en yüksek oyu alanlar” section and carries forward a party label for rows affected by merged cells.
4. For local elections, walks page headings and following `wikitable` tables, then compares parsed vote counts.
5. Converts records into pandas tables, pivots party labels into year columns and writes Excel workbooks.

The script demonstrates web scraping, HTML table interpretation, handling merged-cell structure, reshaping longitudinal tables and Excel output.

## Setup

```bash
python -m pip install requests beautifulsoup4 pandas openpyxl
```

Before running, replace the hard-coded Excel read/write paths with directories on your computer. Province and year lists can be edited near the beginning of each workflow.

**One syntax correction is currently required:** the completion message after the general-election export is missing its closing double quote. Replace that line with:

```python
print("Scraping complete! Data saved to 'ilce_election.xlsx'")
```

After correcting it and setting the paths:

```bash
python WkikipediaScraper.py
```

The `#%%` markers also allow section-by-section execution in an editor that supports Python cells.

## Outputs

| Workbook | Structure |
| --- | --- |
| `ilce_election.xlsx` | Province/district rows; general-election columns such as `WP.1983` |
| `ilce_LOCALelection.xlsx` | Intermediate local-election records |
| `ilce_LOCALelection_pv.xlsx` | Province/district table with local-election columns such as `LE.1984` |

`N/A` represents missing/unparsed records, not a party or a zero vote total. The general-election pivot uses `Province` and `İlçe`; the local-election table uses `Province` and `District`.

## Interpretation and limitations

This is a research script requiring review and adaptation. Its source was inspected for this README update; the scraper was not freshly run against Wikipedia.

- The local-election parser considers headings generically; non-district sections and repeated/irrelevant tables must be filtered and checked before analysis.
- Wikipedia HTML structures and section identifiers can change. A missing table can indicate a parsing mismatch rather than unavailable underlying election data.
- URL suffix handling is hard-coded for selected provinces and differs between the two workflows; failed requests need review.
- Vote parsing strips punctuation and relies on fixed column positions. Check party names, numeric values and winning-party assignments against the page.
- The script does not fully normalize party names, track district-boundary changes or distinguish every election event. Those are separate research preparation steps.
- Existing one-second pauses do not apply to every failure path. Use considerate request rates and keep request/coverage logs for larger runs.

## Source and attribution

Source: [Turkish Wikipedia](https://tr.wikipedia.org/). Retain page URLs, access dates and, ideally, revision identifiers with research extracts. Follow Wikipedia's attribution and content-license requirements when reusing material. Where election accuracy matters, cross-check against original electoral records and the references cited by each page.
