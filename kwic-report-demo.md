Creating a Keyword in Context (KWIC) Report
================

This document explains how to create a KWIC report, which can be useful
for scanning and quickly seeing the terms in a single column, with
preceding text to the left and following text to the right.

## Dependencies

The script is written in R, unlike most of the other code provided in
this collection, which is in Python. R is currently in wide usage and
can be used or run in an R environment, or by using RStudio.

In addition to R, the script uses the following libraries, which can be
installed into your R environment:

- `dplyr`
- `quanteda`
- `readr`

If you need to install these packages, run the following code block:

``` r
# Install packages if needed (run once)
if (!require("readr")) install.packages("readr")
if (!require("quanteda")) install.packages("quanteda")
if (!require("dplyr")) install.packages("dplyr")
```

## Creating the KWIC: the `create_kwic_analysis()` function

The code consists of one main function called `create_kwic_analysis()`.
This function requires a CSV file that is output by the Python code that
parses and identifies term matches from the EAD finding aids; this CSV
must include columns `ead_ID`, `Context`, `Collection`, `Tag`, `Term`.
The function then decomposes this list of term matches into a dataframe
with the desired KWIC format, which can be output in a desired format.

``` r
# implementation of some basic frequency analysis: 
create_kwic_analysis <- function(csv_file_path, window_size = 25, sort_by = "Collection", include_stats = FALSE) {
  # Load required libraries
  require(readr)
  require(quanteda)
  require(dplyr)
  
  # First, we read and prepare the input data
  # The tryCatch helps us handle potential errors when reading the file
  input_data <- tryCatch({
    read_csv(csv_file_path) %>%
      rename(ead_ID = `Occurrence (ead_ID)`)
  }, error = function(e) {
    stop("Error reading or processing CSV file: ", e$message)
  })
  
  # Verify that all required columns are present in the input data
  required_columns <- c("ead_ID", "Context", "Collection", "Tag", "Term")
  missing_columns <- required_columns[!required_columns %in% names(input_data)]
  if (length(missing_columns) > 0) {
    stop("Missing required columns: ", paste(missing_columns, collapse = ", "))
  }
  
  # Create unique document names for each row
  # This ensures we can properly track and identify each context
  unique_docnames <- paste0(input_data$ead_ID, "_", seq_len(nrow(input_data)))
  
  # Create a corpus with metadata
  # The corpus includes both the context and associated metadata like Collection and Tag
  csv_corpus <- corpus(input_data$Context, 
                       docnames = unique_docnames,
                       docvars = data.frame(
                         Collection = input_data$Collection,
                         Tag = input_data$Tag
                       ))
  
  # Create tokens and generate Keywords in Context (KWIC)
  csv_tokens <- tokens(csv_corpus)
  kwic_results <- kwic(csv_tokens, 
                       pattern = input_data$Term,
                       window = window_size)
  
  # Convert results to a data frame and add metadata
  kwic_df <- as.data.frame(kwic_results)
  
  # Add Collection and Tag information from the corpus
  kwic_df$Collection <- docvars(csv_corpus)$Collection[match(kwic_df$docname, docnames(csv_corpus))]
  kwic_df$Tag <- docvars(csv_corpus)$Tag[match(kwic_df$docname, docnames(csv_corpus))]
  
  # Remove duplicate entries based on specified columns
  kwic_df <- kwic_df[!duplicated(kwic_df[c("Tag", "pattern", "from", "to", "pre")]),]
  
  # Arrange columns in the desired order for the final output
  kwic_df <- kwic_df[, c("Collection", "docname", "Tag", "pattern", 
                         "from", "to", "pre", "keyword", "post")]
  
  # Validate the sort_by parameter
  valid_columns <- colnames(kwic_df)
  if (!sort_by %in% valid_columns) {
    warning(paste("Invalid sort column '", sort_by, "'. Available columns are: ",
                  paste(valid_columns, collapse = ", "), 
                  "\nDefaulting to 'Collection'"))
    sort_by <- "Collection"
  }
  
  # Sort the dataframe based on the specified column
  kwic_df <- kwic_df[order(kwic_df[[sort_by]]), ]
  
  # stats
  if (include_stats) {
    # Create summary statistics
    stats <- list(
      # Frequency counts
      keyword_by_collection = table(kwic_df$Collection, kwic_df$keyword),
      keyword_by_tag = table(kwic_df$Tag, kwic_df$keyword),
      
      # Document coverage
      unique_docs = length(unique(kwic_df$docname)),
      docs_per_collection = table(kwic_df$Collection),
      
      # Basic summaries
      total_matches = nrow(kwic_df),
      matches_per_collection = table(kwic_df$Collection),
      matches_per_tag = table(kwic_df$Tag)
    )
    
    return(list(
      results = kwic_df,
      summary_stats = stats
    ))
  }

  # Return the dataframe if stats are not requested
  return(kwic_df)
}
```

## Usage

A few examples of using the code. All assumes running commands from an R
prompt. To run from the console in RStudio, for example, copy the above
function and paste it into the console prompt to load the function.
Then, run the function as in the following examples.

### Create a KWIC report sorted by the matched term

The following reads in a csv to the `csv_file_path` variable, sorts by
the `keyword` column, and outputs the result (a dataframe) to a variable
called `kwic_df_by_keyword`. Then, using `write.csv()`, the
`kwic_df_by_keyword` is saved to the named file.

``` r
# usage, sort by matched term
kwic_df_by_keyword <- create_kwic_analysis(
  csv_file_path = '~/Desktop/encoding-reparative-description/matched_results-philippines.csv',
  include_stats = FALSE,
  sort_by = "keyword"
)

write.csv(kwic_df_by_keyword, file = 'philippine-findingaid-terms-sortby_kw.csv')
```

### Other options

- sort by EAD tag
- create a dataframe some other use
- produce ‘stats’ (overview of some results)
