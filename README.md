# processed-pseudogenes.py

Look for potential processed pseudogenes by applying filters to deletion SVs called by DELLY, then counting the number of events per gene.

Some code adapted from [Ronak Shah's check_cDNA_contamination.py](https://github.com/rhshah/Miscellaneous/blob/master/check_cDNA_contamination.py).


### Usage:
```
usage: python processed_pseudogenes.py [-h] [-s sample_name] [-t n]
                                [--iAnnotateSV iAnnotateSV.py]
                                [--genome {hg18,hg19,hg38}]
                                structural_variants.vcf
                                /path/to/output_directory/

positional arguments:
  structural_variants.vcf
                        VCF with deletion event calls
  /path/to/output_directory/
                        Directory to put output in

optional arguments:
  -h, --help            show this help message and exit
  -s sample_name, --sample-name sample_name
                        Prefix [default: filename of in_vcf]
  -t n, --threshold n   Output pseudogenes must have at least this many
                        qualifying deletion events. [default: 2]
  --iAnnotateSV iAnnotateSV.py
                        path to iAnnotateSV.py script [default: /home/shahr2/g
                        it/iAnnotateSV/iAnnotateSV/iAnnotateSV.py]
  --genome {hg18,hg19,hg38}
                        Reference genome version to use during annotation
                        [default: hg19]
```


### Example usage:
```
$ python processed_pseudogenes.py example.vcf ./ -s my_sample
```


### Output:
- `<output directory>/<sample name>.pseudogenes.txt`: a one-line, tab-delimited list of sample name and its processed pseudogenes
- Intermediate files in `<output directory>/scratch/`


### Requires:
- Python 2
- pandas
- [iAnnotateSV](https://github.com/rhshah/iAnnotateSV) and its required modules
- [iCallSV](https://github.com/rhshah/iCallSV)
- [PyVCF](https://github.com/jamescasbon/PyVCF)

## Script process

1. Filter the input VCF for PRECISE deletions with paired read count > 10
2. Convert the filtered VCF to tab-delimited input format for iAnnotateSV, using iCallSV.dellyVcf2Tab
3. Annotate using iAnnotateSV
4. For each gene, count the number of events that aren't in exons, "in frame" or "out of frame".If a gene has more than the threshold number of events, then conclude it's a processed pseudogene.
