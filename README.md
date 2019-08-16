#Get mean coverage of all genes present in Homo_sapiens.GRCh37.87.gtf from list of sample patients
#gene-coverage

$awk '{if($2=="ensembl_havana" && $3=="CDS") print $20}' Homo_sapiens.GRCh37.87.gtf | sort | uniq |  sed 's/"//g' | sed 's/;//g' > gene_list

$ls FFP_data/*.bam > sample_list

$while read sample; do while read gene; do grep -w "$gene" Homo_sapiens.GRCh37.87.gtf | awk '{if($2=="ensembl_havana" && $3=="CDS") print "chr"$1"\t"$4"\t"$5}' | bedtools intersect -a - -b Truseq_45MB_bed.wo_chrM.bed | bedtools coverage -b "$sample" -a - -d | awk '{print $1"\t"($2+$4-1)"\t"$5}' | sort | uniq | cut -f3 | Rscript -e 'mean(as.numeric(readLines("stdin")))' | awk '{print "'$sample'""\t""'$gene'""\t"$2}' >> result_file; done < gene_list_for_coverage.txt; done < sample_list
