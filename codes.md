# Repeat Maker

RepeatMasker -species human -noint -div 10 -no_is -s /data/test.fa



-no_is         skips bacterial insertion element check
-noint  		When using the -noint or -int option only low complexity DNA and
simple repeats will be masked in the query sequence.
Inexact simple repeats may be spanned and hidden by an interspersed
repeat annotation.

using hg19 GTF file intersect with the Repeat masker to get exonic_Repeats.bed intronic_Repeats.bed intergenic_Repeats.bed UTR_Repeats.bed
## To merge all repeat with full annotation

cat exonic_Repeats.bed intronic_Repeats.bed intergenic_Repeats.bed UTR_Repeats.bed | sed -e 's/(//g' -e 's/)n//g' | awk '{split($4, repeats, "|"); for (i in repeats) {key = $1":"$2":"$3":"i; a[key] = (($3-$2)/length(repeats[i]));}} OFS="\t"{split($4, repeats, "|"); for (i in repeats) {key = $1":"$2":"$3":"i; print $1,$2,$3,repeats[i],a[key],$5,$6}}' | bedtools sort -faidx /staging/human/reference/hg19/dragen_hg19/hg19.fa.fai -i stdin | sed 's/^chr//g' > hg19_uniq_db.txt

# To calculate Haploblock
## to get the pgen 1000 file to bfile in PLINK
plink2 --pgen all_phase3.pgen --pvar all_phase3.pvar --psam all_phase3.psam --max-alleles 2 --make-bed --out all_phase3

## to calculate haploblock

plink --bfile all_phase3 --chr 1-22 XY --allow-extra-chr 0 --geno 0.1 --mind 0.1 --maf 0.05  --make-bed --out base_file

plink --bfile base_file --exclude all_pop/snps.dupvar --threads 40 --blocks-strong-lowci 0.7 --blocks no-pheno-req --blocks-max-kb 2000 --out all_pop_haploblock
