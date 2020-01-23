# vcf_liftover

Forked from [lift-over-vcf](https://github.com/knmkr/lift-over-vcf) with improvements including:
- hg38ToHg19 support
- stderr reporting of unmapped/failed coordinates
- fix `--no-check-certificate` error on install

## Install

```
git clone https://github.com/mvelinder/vcf_liftover.git
cd vcf_liftover
bash install.sh
```

## Usage

Works on uncompressed and compressed vcfs, simply change your (z)cat

```
zcat $VCF | python ~/bin/vcf_liftover/lift_over.py --chain $CHAIN > $VCF.liftover.vcf 2>$VCF.liftover.vcf.err
```

available `--chain` arguments are: `hg18ToHg19 hg18ToHg38 hg19ToHg38 hg38ToHg19`

#### hg19ToHg38
```
zcat $hg19VCF | python ~/bin/vcf_liftover/lift_over.py --chain hg19ToHg38 > $hg19VCF.liftover.hg38.vcf 2>$hg19VCF.liftover.hg38.vcf.err
```

#### hg38ToHg19
```
zcat $hg38VCF | python ~/bin/vcf_liftover/lift_over.py --chain hg38ToHg19 > $hg38VCF.liftover.hg19.vcf 2>$hg38VCF.liftover.hg19.vcf.err
```

### Error reporting

Unmapped coordinates are reported in stderr, which above we're redirecting to our `.err` file. All unmapped records start with `#` and are in the following example format `# mapping failed at chr1:181499`. Easily retrieve all failed records with something like `grep ^# $VCF.liftover.err`

### Testing and performance

Platinum [genome](ftp://platgene_ro@ussd-ftp.illumina.com/2017-1.0/) VCFs containing 1000 variants on chr21 are available for testing in `test/`

Expect performance along the lines of 36 variants per second:
```
$ time zcat NA12878.hg19.test.vcf.gz | python ~/bin/vcf_liftover/lift_over.py --chain hg19ToHg38 > NA12878.hg19.test.vcf.gz.hg38.vcf 2>NA12878.hg19.test.vcf.gz.hg38.vcf.err

real	0m27.984s
user	0m9.523s
sys	0m5.803s
```
