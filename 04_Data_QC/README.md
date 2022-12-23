# 04 Plink basics

In this module, we will learn the basics of genotype data QC using PLINK, which is one of the most commonly used software in complex trait genomics.


### Table of Contents
- [4.1 Preparation](#41-preparation)
	- [PLINK 1.9 & 2 installation](#411-plink-192-installation)
	- [Simulated data download](#412-simulated-data-download)
- [4.2 PLINK tutorial](#42-plink-tutorial)
	- [Calculate missing rate and call rate](#421-missing-rate-and-call-rate)
	- [Calculate Allele Frequency](#422-allele-frequency)
	- [Calculate inbreeding F coefficient ](#423-inbreeding-f-coefficient)
	- [Hardy-Weinberg equilibrium exact test](#424-hardy-weinberg-equilibrium-exact-test)
	- [Applying filters](#425-applying-filters)
	- [LD-Pruning](#426-pruning)
	- [Sample & SNP filtering (extract/exclude/keep/remove)](#427-sample--snp-filtering-extractexcludekeepremove)
	- [LD calculation](#429-ld-calculation)
	- [Estimate IBD / PI_HAT](#428-ibd--pi_hat)
	- [Data management (make-bed/recode)](#4210-data-management-make-bedrecode)
- [Exercise](#exercise)
- [Additional resources](#additional-resources)
- [Reference](#reference)

## 4.1 Preparation
### 4.1.1 PLINK 1.9&2 installation
### Make directories and add to envrionment path
First, we will simply create directories to keep the tools we need to use.
```bash
cd ~
mkdir tools
cd tools
mkdir bin
mkdir plink
mkdir plink2
```

You can download each tool into its corresponding directories. The `bin` directory here is for keeping all the symbolic links to the executable files of each tool. 

In this way, it is much easier to manage the path and tools.
<img width="937" alt="image" src="https://user-images.githubusercontent.com/40289485/160745597-c6f4204a-d786-4af1-9041-f1531cbbe584.png">

### Download PLINK1.9 and PLINK2 and then unzip
Next, go to the plink webpage to download the software. We will need both PLINK1.9 and PLINK2.
Download PLINK1.9 and PLINK2 from the following webpage to the corresponding directories:
https://www.cog-genomics.org/plink/
https://www.cog-genomics.org/plink/2.0/

Note: If you are using mac or windows, then please download the mac or windows version. 
In this tutorial we use a linux system, so we will shoe the codes for the linux version. 

Find the suitable version, right click and copy the link address.
```bash
cd plink2
wget https://s3.amazonaws.com/plink2-assets/plink2_linux_x86_64_20211011.zip
unzip plink2_linux_x86_64_20211011.zip
```
Then do the same for PLINK1.9

### Create symbolic links
After donwloading and unzipping, we will create symbolic links for the plink binany files, and then move the link to `~/tools/bin/`.
```bash
cd ~
ln -s ~/tools/plink2/plink2 ~/tools/bin/plink2
ln -s ~/tools/plink/plink ~/tools/bin/plink
```
Then add `~/tools/bin/` to the environment path.
```bash
export PATH=$PATH:~/tools/bin/
```
And then run 
```
echo "export PATH=$PATH:~/tools/bin/" >> ~/.bashrc
```
This will add a new line at the end of `.bashrc`, which will be run every time you open a new bash shell.

All done. Let's test if we installed PLINK successfully or not.

```bash
plink -h
plink2 -h
```
### PLINK1.9
```
plink -h
PLINK v1.90b6.21 64-bit (19 Oct 2020)          www.cog-genomics.org/plink/1.9/
(C) 2005-2020 Shaun Purcell, Christopher Chang   GNU General Public License v3

  plink <input flag(s)...> [command flag(s)...] [other flag(s)...]
  plink --help [flag name(s)...]

Commands include --make-bed, --recode, --flip-scan, --merge-list,
--write-snplist, --list-duplicate-vars, --freqx, --missing, --test-mishap,
--hardy, --mendel, --ibc, --impute-sex, --indep-pairphase, --r2, --show-tags,
--blocks, --distance, --genome, --homozyg, --make-rel, --make-grm-gz,
--rel-cutoff, --cluster, --pca, --neighbour, --ibs-test, --regress-distance,
--model, --bd, --gxe, --logistic, --dosage, --lasso, --test-missing,
--make-perm-pheno, --tdt, --qfam, --annotate, --clump, --gene-report,
--meta-analysis, --epistasis, --fast-epistasis, and --score.

"plink --help | more" describes all functions (warning: long).
```
### PLINK2
```
plink2 -h
PLINK v2.00a3LM 64-bit Intel (1 Jul 2021)      www.cog-genomics.org/plink/2.0/
(C) 2005-2021 Shaun Purcell, Christopher Chang   GNU General Public License v3

  plink2 <input flag(s)...> [command flag(s)...] [other flag(s)...]
  plink2 --help [flag name(s)...]

Commands include --rm-dup list, --make-bpgen, --export, --freq, --geno-counts,
--sample-counts, --missing, --hardy, --het, --fst, --indep-pairwise, --ld,
--sample-diff, --make-king, --king-cutoff, --pmerge, --pgen-diff,
--write-samples, --write-snplist, --make-grm-list, --pca, --glm, --adjust-file,
--score, --variant-score, --genotyping-rate, --pgen-info, --validate, and
--zst-decompress.

"plink2 --help | more" describes all functions.

```
Well done. We have installed plink1.9 and plink2.


### 4.1.2 simulated data download
Next we need to download the sample genotype data

Run the download.sh in 01_Dataset

```
cd ../01_Dataset
./download.sh
```
```
-rw-r-----   1 he  staff   135M Dec 23 11:45 1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020.bed
-rw-r-----   1 he  staff    36M Dec 23 11:46 1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020.bim
-rw-r-----   1 he  staff   9.4K Dec 23 11:46 1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020.fam
```

check bim

```bash
head 1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020.bim
```

Output:

```
1	1:13273:G:C	0	13273	C	G
1	1:14599:T:A	0	14599	A	T
1	1:14604:A:G	0	14604	G	A
1	1:14930:A:G	0	14930	G	A
1	1:69897:T:C	0	69897	C	T
1	1:86331:A:G	0	86331	G	A
1	1:91581:G:A	0	91581	A	G
1	1:122872:T:G	0	122872	G	T
1	1:135163:C:T	0	135163	T	C
1	1:233473:C:G	0	233473	G	C
```

check fam
```bash
head 1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020.fam
```

Output:

```
0 HG00403 0 0 0 -9
0 HG00404 0 0 0 -9
0 HG00406 0 0 0 -9
0 HG00407 0 0 0 -9
0 HG00409 0 0 0 -9
0 HG00410 0 0 0 -9
0 HG00419 0 0 0 -9
0 HG00421 0 0 0 -9
0 HG00422 0 0 0 -9
0 HG00428 0 0 0 -9
```

## 4.2 PLINK tutorial
Detailed description can be found on plink's website: 

https://www.cog-genomics.org/plink/2.0/ and https://www.cog-genomics.org/plink/1.9/

The functions we will learn in this tutorial:
1. Missing rate/ call rate
2. Inbreeding F coefficient
3. Hardy-Weinberg equilibrium exact test
4. Allele Frequency
5. Applying filters
6. Pruning
7. Sample & snp filtering (extract/exclude/keep/remove)
8. IBD / PI_HAT
9. LD calculation
10. Data management (make-bed/recode)

All sample codes and results for this module are available in `./04_data_QC`

First, we can calculate some basic statistics of our simulated data:
### 4.2.1 missing rate and call rate
The first thing we want to know is the missing rate of our data. Usually we need to check the missing rate of samples and snps to decide a threshold to exclude low-quality samples and snps. (https://www.cog-genomics.org/plink/1.9/basic_stats#missing)

The input is PLINK bed/bim/fam file. Usually they have the same prefix, and we just need to pass the prefix to `--bfile` option.

### PLink syntax:
![image](https://user-images.githubusercontent.com/40289485/161413684-a128b87f-fb79-4b13-a7b5-acfa997c4421.png)

Sample code to calculate missing rate

```bash
cd ../04_Data_QC
genotypeFile="../01_Dataset/1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020" #!!! Please add your own path here.  "1KG.EAS.auto.snp.norm.nodup.split.maf005.thinp020" is the prefix of PLINK bed file. 

plink \
	--bfile ${genotypeFile} \
	--missing \
	--out plink_results
```
Remeber to set the value for `${genotypeFile}`.

This code will generate two files `plink_results.imiss` and `plink_results.lmiss`, which contain the missing rate information for samples and snps respectively.

Take a look at the imiss file. The last column shows the missing rate for samples. Since we used simulated data this time, there are no missing samples or snps. So the missing rate is zero.
```bash
head plink_results.imiss
```

Output:

```
 FID       IID MISS_PHENO   N_MISS   N_GENO   F_MISS
   0   HG00403          Y        0  1122299        0
   0   HG00404          Y        0  1122299        0
   0   HG00406          Y        0  1122299        0
   0   HG00407          Y        0  1122299        0
   0   HG00409          Y        0  1122299        0
   0   HG00410          Y        0  1122299        0
   0   HG00419          Y        0  1122299        0
   0   HG00421          Y        0  1122299        0
   0   HG00422          Y        0  1122299        0
```

### 4.2.2 Allele Frequency

One of the most important statistics of SNPs are their frequencies in a certain population. A number of downstream analysis is based on investigating differences in the allele frequencies.

Usually, variants can be categorized into 3 groups based on their minor allele frequency:
1. Common variants : MAF>=0.05
2. Low-frequency variants : 0.01<=MAF<0.05
3. Rare variants : MAF<0.01

Using PLINK1.9 we can easily calculate the MAF (Minor Allel Frequency) of variants in the input data.

```bash
plink \
	--bfile ${genotypeFile} \
	--freq \
	--out plink_results
```

Next we use plink2 to run the same options to check the difference between the results.
```bash
plink2 \
        --bfile ${genotypeFile} \
        --freq \
        --out plink_results
```

```bash
# results from plink1.9
head plink_results.frq
```

Output:

```
 CHR              SNP   A1   A2          MAF  NCHROBS
   1      1:13273:G:C    C    G       0.0625     1008
   1      1:14599:T:A    A    T      0.08929     1008
   1      1:14604:A:G    G    A      0.08929     1008
   1      1:14930:A:G    G    A       0.4137     1008
   1      1:69897:T:C    T    C        0.124     1008
   1      1:86331:A:G    G    A       0.0873     1008
   1      1:91581:G:A    A    G        0.498     1008
   1     1:122872:T:G    G    T       0.2589     1008
   1     1:135163:C:T    T    C      0.09226     1008
```

```bash
# results from plink2
head plink_results.afreq
```

```
#CHROM	ID	REF	ALT	ALT_FREQS	OBS_CT
1	1:13273:G:C	G	C	0.0625	1008
1	1:14599:T:A	T	A	0.0892857	1008
1	1:14604:A:G	A	G	0.0892857	1008
1	1:14930:A:G	A	G	0.41369	1008
1	1:69897:T:C	T	C	0.875992	1008
1	1:86331:A:G	A	G	0.0873016	1008
1	1:91581:G:A	G	A	0.498016	1008
1	1:122872:T:G	T	G	0.258929	1008
1	1:135163:C:T	C	T	0.0922619	1008
```
We need pay attention to the concepts here.

In PLINK1.9, the concept here is minor (A1) and major(A2) allele, while in PLINK2 it is reference(REF) and altervative(ALT) allele.
### Major / Minor
Major allele and minor allele are defined as the allele with highest and lower(or second highest for multiallelic variants) allele in a given population, respectively. So major allele and minor allele for a SNP might be different in two independent populations. The range for MAF(minor allele frequencies) is [0,0.5].
### Ref / Alt
Reference(REF) and altervative allele simply refers to the allele on a reference genome. If we use the same reference genome, the reference(REF) and altervative(ALT) allele will be the same acorss populations. Reference allele could be major or minor in different populations. The range for alternative allele frequency is [0,1], since it could be major or minor allele in a given population.


### 4.2.3 Inbreeding F coefficient 
Next we can check the heterozygosity F of samples (https://www.cog-genomics.org/plink/1.9/basic_stats#ibc) : 

```bash
plink \
	--bfile ${genotypeFile} \
	--het \
	--out plink_results
```
```bash
head plink_results.het
```

Output:

```
 FID       IID       O(HOM)       E(HOM)        N(NM)            F
   0   HG00403       747270    7.488e+05      1122299    -0.004114
   0   HG00404       748955    7.488e+05      1122299    0.0003974
   0   HG00406       750093    7.488e+05      1122299     0.003444
   0   HG00407       746566    7.488e+05      1122299    -0.005999
   0   HG00409       751694    7.488e+05      1122299     0.007731
   0   HG00410       745078    7.488e+05      1122299    -0.009983
   0   HG00419       747996    7.488e+05      1122299     -0.00217
   0   HG00421       757199    7.488e+05      1122299      0.02247
   0   HG00422       752725    7.488e+05      1122299      0.01049
```


### 4.2.4 Hardy-Weinberg equilibrium exact test
For SNP QC, besides checking the missing rate, we also need to check if the SNP is in hardy-weinberg equilibrium:
The following command can calculate the Hardy-Weinberg equilibrium exact test statistics for all SNPs. (https://www.cog-genomics.org/plink/1.9/basic_stats#hardy)
```bash
plink \
	--bfile ${genotypeFile} \
	--hardy \
	--out plink_results
```
```bash
head plink_results.hwe
```

Output:

```
 CHR              SNP     TEST   A1   A2                 GENO   O(HET)   E(HET)            P 
   1      1:13273:G:C  ALL(NP)    C    G             1/61/442    0.121   0.1172       0.7113
   1      1:14599:T:A  ALL(NP)    A    T             1/88/415   0.1746   0.1626       0.1625
   1      1:14604:A:G  ALL(NP)    G    A             1/88/415   0.1746   0.1626       0.1625
   1      1:14930:A:G  ALL(NP)    G    A             4/409/91   0.8115   0.4851    1.679e-61
   1      1:69897:T:C  ALL(NP)    T    C            7/111/386   0.2202   0.2173            1
   1      1:86331:A:G  ALL(NP)    G    A             0/88/416   0.1746   0.1594      0.02387
   1      1:91581:G:A  ALL(NP)    A    G          137/228/139   0.4524      0.5      0.03271
   1     1:122872:T:G  ALL(NP)    G    T            1/259/244   0.5139   0.3838     8.04e-19
   1     1:135163:C:T  ALL(NP)    T    C             1/91/412   0.1806   0.1675       0.1066
```



### 4.2.5 Applying filters
Previously we just calculate the basic statistics using PLINK. But when we want to actually analyze the data, we want to exclude some bad quallity sample or snps.
In this case we can apply the following filters (for example):

`--maf 0.01` : exlcude snps with maf<0.01

`--geno 0.01` :filters out all variants with missing rates exceeding 0.01

`--mind 0.02` :filters out all samples with missing rates exceeding 0.02

`--hwe 5e-6` : filters out all variants which have Hardy-Weinberg equilibrium exact test p-value below the provided threshold. NOTE: With case/control data, cases and missing phenotypes are normally ignored. (see https://www.cog-genomics.org/plink/1.9/filter#hwe)

### 4.2.6 Pruning
Since there are ofter strong LD among SNPs, for some analysis we don't need all SNPs.
We can use `--indep-pairwise 50 5 0.2` to filter out those are in strong LD and keep only the independent SNPS.
Please check https://www.cog-genomics.org/plink/1.9/ld#indep for the meaning of each parameter.
Combined with the filters we just introduced, we can run:

```bash
plink \
	--bfile ${genotypeFile} \
	--maf 0.01 \
	--geno 0.01 \
	--mind 0.02 \
	--hwe 5e-6 \
	--indep-pairwise 50 5 0.2 \
	--out plink_results
```
This command generates two outputs :  `plink_results.prune.in` and `plink_results.prune.out`
`plink_results.prune.in` is the independent set of SNPs we will use in the following analysis.
Let's take a look at this file. Basically it just contains one SNP id per line.
```bash
head plink_results.prune.in
```

Output:

```
1:13273:G:C
1:14599:T:A
1:69897:T:C
1:86331:A:G
1:91581:G:A
1:135163:C:T
1:233473:C:G
1:532929:T:C
1:559480:C:T
1:565433:C:T
```

### 4.2.7 sample & snp filtering (extract/exclude/keep/remove)
Some time we don't need all the samples or snps included the original input file. 
In this case, we can use `--extract` or `--exclude` to select or exclude SNPs from analysis, `--keep` or `--remove` to select or exclude samples.

For  `--keep` or `--remove` , the input is the filename of a sample FID and IID file.
For `--extract` or `--exclude` , the input is the filename of a snplist file.
```bash
head plink_results.prune.in
```

Output:

```
1:13273:G:C
1:14599:T:A
1:69897:T:C
1:86331:A:G
1:91581:G:A
1:135163:C:T
1:233473:C:G
1:532929:T:C
1:559480:C:T
1:565433:C:T
```

### 4.2.8 IBD / PI_HAT
`--genome` invokes an IBS/IBD computation. Usually for this analysis, we need to prune our data first since the strong LD will cause bias in the results.
(This step is extremelly computationally intensive, so we only calculate  IBD for the first 1000 samples)
Combined with the `--extract` and `--keep` option, we can run:
```bash
plink \
        --bfile ${genotypeFile} \
	--extract plink_results.prune.in \
	--keep first_1000.sample \
        --genome \
        --out plink_results
```
PI_HAT is the IBD estimation. Please check https://www.cog-genomics.org/plink/1.9/ibd for more details.
```bash
head plink_results.genome
```

Output:

```
 FID1     IID1 FID2     IID2 RT    EZ      Z0      Z1      Z2  PI_HAT PHE       DST     PPC   RATIO
   0  HG00403   0  HG00404 OT     0  0.9800  0.0086  0.0114  0.0157  -1  0.749375  0.5531  2.0089
   0  HG00403   0  HG00406 OT     0  0.9751  0.0231  0.0018  0.0133  -1  0.748336  0.6309  2.0225
   0  HG00403   0  HG00407 OT     0  0.9801  0.0153  0.0046  0.0122  -1  0.748293  0.5045  2.0007
   0  HG00403   0  HG00409 OT     0  0.9807  0.0193  0.0000  0.0097  -1  0.747215  0.6014  2.0173
   0  HG00403   0  HG00410 OT     0  0.9744  0.0256  0.0000  0.0128  -1  0.748058  0.8621  2.0745
   0  HG00403   0  HG00419 OT     0  0.9650  0.0350  0.0000  0.0175  -1  0.747146  0.8080  2.0595
   0  HG00403   0  HG00421 OT     0  0.9842  0.0158  0.0000  0.0079  -1  0.746522  0.4557  1.9926
   0  HG00403   0  HG00422 OT     0  0.9832  0.0111  0.0056  0.0112  -1  0.748156  0.4208  1.9867
   0  HG00403   0  HG00428 OT     0  0.9929  0.0000  0.0071  0.0071  -1  0.746851  0.3115  1.9674
```

### 4.2.9 LD calculation
We can use our data to calculate LD between a pair of SNPs.

![image](https://user-images.githubusercontent.com/40289485/161413586-d4f6e21e-f0c7-4c54-a703-bb060ec6913d.png)


`--chr` option allows us to include snps on a specific chromosome.
To calculate LD r2 for SNPs on chr22 , we can run:
```bash
plink \
	--bfile ${genotypeFile} \
        --chr 22 \
        --r2 \
        --out plink_results
```
```bash
head plink_results.ld
```

Output:

```
 CHR_A         BP_A             SNP_A  CHR_B         BP_B             SNP_B           R2 
    22     16053659   22:16053659:A:C     22     16067500   22:16067500:T:C      0.64577 
    22     16053862   22:16053862:C:T     22     16054454   22:16054454:C:T     0.987505 
    22     16053862   22:16053862:C:T     22     16055122   22:16055122:G:T     0.601708 
    22     16053863   22:16053863:G:A     22     16055070   22:16055070:G:A     0.931043 
    22     16054454   22:16054454:C:T     22     16055122   22:16055122:G:T     0.594216 
    22     16058767   22:16058767:A:G     22     16067462   22:16067462:C:T     0.266916 
    22     16067462   22:16067462:C:T     22     16069771   22:16069771:G:A     0.382323 
    22     16069771   22:16069771:G:A     22     16123813   22:16123813:G:A     0.291935 
    22     16143946   22:16143946:A:G     22     16155259   22:16155259:A:G     0.559165 
```

### 4.2.10 data management (make-bed/recode)
By far the input data we use is in binary form, but sometimes we may want the text version.
![image](https://user-images.githubusercontent.com/40289485/161413659-a489b508-63c7-4166-9f5c-25a1a125109a.png)


To convert the format we can run:

```bash
#extract the 1000 samples with the pruned SNPs, and make a bed file.
plink \
	--bfile ${genotypeFile} \
	--extract plink_results.prune.in \
	--make-bed \
	--out plink_1000_pruned

#convert the bed/bim/fam to ped/map
plink \
        --bfile plink_1000_pruned \
        --recode \
        --out plink_1000_pruned
```

## Exercise
- Follow this tutorial and type in the commands:
  - Calculate basic statistics for the simulated data.
  - Learn the meaning of each QC step.

- Visualize the results of QC (using Python or R)
  - Draw the distribution of MAF.(histogram)
  - Draw the distribution of het.(histogram)
  - Try to briefly explain what you observe

## Additional resources
- Marees, A. T., de Kluiver, H., Stringer, S., Vorspan, F., Curis, E., Marie‐Claire, C., & Derks, E. M. (2018). A tutorial on conducting genome‐wide association studies: Quality control and statistical analysis. International journal of methods in psychiatric research, 27(2), e1608.

## Reference
- Purcell, S., Neale, B., Todd-Brown, K., Thomas, L., Ferreira, M. A., Bender, D., ... & Sham, P. C. (2007). PLINK: a tool set for whole-genome association and population-based linkage analyses. The American journal of human genetics, 81(3), 559-575.
- Chang, C. C., Chow, C. C., Tellier, L. C., Vattikuti, S., Purcell, S. M., & Lee, J. J. (2015). Second-generation PLINK: rising to the challenge of larger and richer datasets. Gigascience, 4(1), s13742-015.