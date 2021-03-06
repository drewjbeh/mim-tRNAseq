mim-tRNAseq introduction
========================

Method strategy
^^^^^^^^^^^^^^^

The driving force behind this pipeline is the unique alignment strategy it uses to accurately place tRNA-seq reads during alignment. The method is based off the ability of a reverse transcriptase (RT), TGIRT, to misincorportate incorrect nucleotides at modified tRNA positions. 

Due to the abundance of modifications to tRNA residues as well as high sequence conservation between tRNA genes and isodecoders, the generation and alignment of tRNA sequencing datasets is faced with two problems: 1) the modifications cause normal RTs to fail during the cDNA synthesis step of RNA-seq library preparation, and 2) even when long enough reads are sequenced, they cannot be uniquely placed during alignment because of the high degree of sequence similarity between tRNAs. This results in high rates of multi-mapping reads which are difficult to use for downstream analysis.

Using mim-tRNAseq and TGIRT overcomes these problems. First, tRNA genes sharing anticodons are clustered according to a user-defined ID threshold to limit read placement ambiguity during alignment. Next, the misincorporation by TGIRT at modified nucleotides allows read-through of modifications producing longer reads and libraries with less biased tRNA coverage, further improving alignment statistics and quantification. Additionally, indexed modification data is used in SNP-tolerant alignments by GSNAP (Wu and Nacu, 2010) to account for misincorporations, and thereby guide more accurate read placement. Collectively, mim-tRNAseq improves tRNA gene coverage from sequencing data and thereby reduces bias, enables more data from libraries to be used by reducing multi-mapping, and overall improves estimation of tRNA expression.

Detailed methodology is shown in the image below, and described in `Behrens <https://doi.org/10.1016/j.molcel.2021.01.028>`_ et al., 2021, Molecular Cell 81, 1–14

.. image:: ../img/method.png

Since v0.3, the deconvolution algorithm has been updated to consider all mismatches as a set for each unique isodecoder instead of individual mismatches. This theoretically allows the distinction and deconvolution of all unique tRNA sequences, since by definition each unique sequence has a distinct set of mismatches. Despite this, some clusters still have 3' coverage bias due to modifications that induce to stops to RT. In these cases, complete deconvolution of reads for these sequences might not be possible if coverage at a mismatch defining the sequence is significanly lower than at the 3' end of the tRNA (in this case, the number of reads assigned to this sequence will be underestimated). In order to overcome this, the *--deconv-cov-ratio* paramater can be used to set a threshold for this difference in coverage. Sequences not passing this threshold will be marked as un-deconvoluted. Note that sequences not deconvoluted, as well as those not passing *--min-cov* are excluded from all modification plots.
