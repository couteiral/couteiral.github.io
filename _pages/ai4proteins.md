---
layout: extended-content
title: "AI for protein structure: past, present and future"
permalink: /ai4proteins
usemathjax: true
---

__Last updated: March 2023__

1. [Background: why do we care about protein structure?](#background)
2. [A little bit of history](#history)
3. [Exploiting sequence data](#coevolution)
4. [Deep learning and structure prediction](#deeplearning)
5. [An introduction to AlphaFold 2](#alphafold)
6. [Advances over AlphaFold](#beyond-alphafold)
7. [An overview of doors opened](#what-is-now-possible)
8. [Open challenges](#open-challenges)
9. [Conclusions](#conclusions)
10. [Bibliography](#bibliography)

<i style="font-size: 16px; line-height: 1.0; font-weight: 50">I started these lecture notes to prepare for lectures in AI for protein structure at the Systems Aproaches to Biological Science Doctoral Training Centre, and at the 58th International School of Crystallography. One thing led to the other and I found myself having written twenty pages of lecture notes and over 100 references. The career-savvy move would have been to publish it as a review, but as I suspect it would have been outdated by the time it was on print, I instead decided to publish it as a blog post that I keep continuously updating. I hope this is useful. If you spot any errors, please contact me.</i>

<a name="background"></a>
### 1. Background: why do we care about protein structure?

Proteins are intricate molecular machines whose function underlies all
of life. Almost any impressive behaviour of biological systems -- how
our eyes transform light into nerve impulses, how our immune systems
recognise and fend off potential invaders, or how our muscles convert
chemical energy into physical force -- ultimately relies on the ability
of proteins to catalyse chemical reactions, transport nutrients,
transduct signals or even serve as a structural building blocks in
complex scaffolds. Proteins are made up of amino acids, which are linked
together in long chains to form a polypeptide. There are twenty natural
or *proteinogenic* amino acids, each with a characteristic chemical
behaviour -- meaning that proteins can leverage all kind of chemical
groups to exert their function. The enormous biochemical versatility of
proteins has meant they are involved in virtually every aspect of
cellular life and are the focus of intense research across many
scientific disciplines.

The sequence of amino acids in a protein uniquely determines its
three-dimensional structure, which in turn dictates its function in the
cell. For example, structural proteins like collagen and keratin
assemble in extended, braided scaffolds that resemble ropes and confer
enormous resilience to the tissues they cover. Transporters like
hemoglobin implement "switches" that alternate between different states:
one with avidity for the transported molecule (for example, the
"relaxed" state of hemoglobin binds oxygen strongly in the lungs) and
another which binds it less strongly (for example, the "tense" state
hemoglobin has little affinity for oxygen and releases it in the
tissues). Enzymes, on the other hand, have active sites which provide a
chemical environment easing the hardest steps in a chemical reaction,
thus optimising the process. Understanding the relationship between
protein structure and function is a fundamental problem in molecular
biology and is essential for developing treatments for a wide range of
diseases.

Determining the structure of a protein is, unfortunately, very
challenging. As of March 2023, there were over 230 million protein
sequences deposited in the TrEMBL database {% cite uniprot2019uniprot %}, but
given the difficulties in experimentally characterising protein
structure, little over 200,000 structures were available in the Protein
DataBank {% cite berman2000protein %}. Bridging the gap between sequence and
structure availability enables exploring the functional landscape of
these proteins, and is a fundamental step to understand the connection
between genotype (which determines the sequence of the protein) and
phenotype (the observable, physical and macroscopic behaviours
determined by the genetic information). This pursuit has motivated
significant efforts to develop computational methods able to produce
accurate predictions of protein structure directly from sequence. The
progress of this pursuit is the focus of this lecture notes.

In more practical terms, here are a few problems where knowing a
protein's structure is paramount:

-   **Drug discovery**. Most drugs target proteins, either by blocking
    something they do, or interacting with them and making them change
    their ways. One of the most successful approaches to find new drugs
    is structure-based drug discovery {% cite jhoti2007structure %}, where a
    molecule is designed to fit within the desired binding pocket in a
    protein. A classical hurdle in structure-based drug discovery has
    been access to a good enough structure of the protein target
    {% cite young2009computational %}.

-   **Molecular simulations**. Our tools to investigate the behaviour of
    biomolecules at the molecular level -- how they interact, how they
    change, how they form part of complex systems, etc. -- are very
    limited. This gap is typically filled by simulations, often using
    molecular dynamics {% cite rapaport2004art %}. However, to sample the
    phenomena we are interested in, these simulations require an initial
    structure that closely resembles the process we want to study.

-   **Evolutionary studies**. Genetic information is subject to
    mutations, meaning that sequences rapidly diverge -- but as protein
    structure is indispensable for function, it tends to be much more
    strongly conserved. Understanding how protein structure varies
    across the tree of life allows unearthing complex evolutionary
    pressures and relations.

-   **Protein design**. One of the most interesting pursuits in modern
    synthetic biology is protein design, where an artificial protein is
    devised to out some desired function. For example, while there are
    many types of natural enzymes, there are many reactions for which no
    natural enzymes are known. Designing artificial enzymes for these
    reactions would significantly enhance the production of many
    valuable chemicals. A key challenge in coming up with enzymes, and
    other proteins is to validate whether the proposed protein sequences
    fold to the desired structure.

-   **Property predictions**. Many interesting properties of proteins
    are highly dependent on the structure of the protein. Protein
    stability, solubility, function, and many others, depend on the
    structure of the protein, often because they result from the
    thermodynamics of the protein, which are strongly governed by the
    structure of the native state.

These arguments support the importance of predicting the structure of a
protein from its amino acid sequence. In the next sections, we will
explore how this can be done.

<a name="history"></a>
### 2. A little bit of history

Protein structure prediction methods have traditionally been divided
into two main areas. The first family of methods, known as
*template-based modelling* (TBM), rely on finding a protein structure
that has a structure very similar to the target sequence; we say that
this new protein has *a high degree of homology* to our original
molecule. The rationale is that, since protein structure is a lot more
conserved than sequence, many diverse protein sequences will fold to
similar structures, increasing the chance that someone, somewhere, has
identified a similar structure before. Sequence similarity is often used
as a proxy for homology, and it has generally been accepted that
proteins with over 25-30% sequence identity have enough homology to be
similar in structure {% cite pearce2021toward %}. After a good reference
structure, known as a *template*, has been identified, it is used as an
initial guess and refined to account for the differences with the
target. The first successful attempt to predict the three-dimensional
structure of a protein was an example of template-based modelling: the
experimental X-ray structure of hen-egg lysozyme was used to propose a
conformation for bovine alpha-lactalbumin, a protein with 40% sequence
identity to the former {% cite browne1969possible %}. Template-based modelling
has been considered generally successful, even for complex applications
like structure-based drug discovery {% cite young2009computational %}, whenever
good templates were available.

*Template-free modelling* or *free modelling* (FM) attempts to predict a
protein's structure without a suitable template, often because one is
not available. These methods have invariably been inspired by Anfinsen's
thermodynamic hypothesis that the native structure of a protein
corresponds to the lowest free energy conformation of the chain. Free
modelling protocols typically use a custom score, for example the
Rosetta energy function {% cite alford2017rosetta %}, and employ an optimisation
algorithm to find conformations that minimise said score. These methods
have tended to be highly diverse, including approaches that tend towards
*ab initio* protocols inspired in molecular dynamics
{% cite oldziej2005physics %}, and hybrid methods using knowledge-based
potentials. Although free modelling has traditionally been recognised as
different than template-based modelling, in practice concepts and
methods have permeated between the two. For example, fragment generation
processes for free modelling have tended to use methods very similar to
threading and template search (and similarly template-based modelling
has inherited energy functions and minimization procedures from free
modelling). Nevertheless, free modelling has remained relatively
unsuccessful and has only recently (in the early 2020s) become accurate
enough to be generally useful.

Progress in structure prediction has been driven and measured by the
*Critical Assessment of protein Structure Prediction* (CASP), a
biennial, community-led experiment. In every edition of CASP,
independent research groups in protein structure prediction are asked to
predict the structures of several proteins that have been determined
experimentally, but not made public at the time of the exercise.
Comparison of predictions and experimental results, evaluated by an
independent team of assessors, produces an unbiased estimate of the
state of the field. CASP is not only a *bona fide* appraisal of the
state-of-the-art, but also an opportunity for the latest advances to
permeate throughout the community. The CASP assessment exercise has
witnessed multiple step changes in accuracy as novel ideas were
incorporated into the participant's pipelines. For example, significant
progress was made between CASP4 and CASP5 as a result of the widespread
use of metaserver refinement {% cite moult2005decade %}, and the success of deep
learning in CASP13 led to generalised adoption of this technique in
CASP14 {% cite pereira2021high %}.

One important idea in template-free structure prediction is *fragment
replacement*, an algorithm proposed by Bowie and Eisenberg
{% cite bowie1994evolutionary %} in 1994, the same year that CASP was initiated.
Rather than trying to explore the full energetic landscape, the authors
proposed a stochastic search where small peptide fragments sampled from
a curated structural database were randomly replaced at set positions of
the protein, to rapidly change the conformation. After each
substitution, conformations could be scored with a custom potential, and
accepted or rejected with a Monte Carlo criterion. The authors'
intuition was that, whereas structure databases are unlikely -- even
today -- to have explored every possible protein fold, the local
conformations of small groups of amino acids have far fewer available
conformations, and are probably more thoroughly sampled. This approach
was highly competitive with other methods
{% cite duan1998pathways; @moult2005decade %}. Fragment replacement was a key
step in the popular protein struture prediction method Rosetta
{% cite simons1997assembly %}, which was the top performer in most CASPs until
recent editions, and remains widely used at the time of writing.

<a name="casp14_historic"></a>
![**Historical evaluation of progress throughout the fourteen editions
of CASP.** The vertical axis displays the global distance test
{% cite zemla2003lga %}, a measure of structural similarity that is less
sensitive to large distortions in small portions of the protein than the
more common RMSD; and the horizontal axis displays target difficulty, in
terms of the number of available templates. This figure has been taken
from John Moult's keynote at the CASP14
conference.](assets/images/johnmoult.png)
**Figure 1. Historical evaluation of progress throughout the fourteen editions
of CASP.** The vertical axis displays the global distance test
{% cite zemla2003lga %}, a measure of structural similarity that is less
sensitive to large distortions in small portions of the protein than the
more common RMSD; and the horizontal axis displays target difficulty, in
terms of the number of available templates. This figure has been taken
from John Moult's keynote at the CASP14
conference.

Another central idea was predicting pairs of residues spatially close in
the structure of the protein and incorporating them as an optimisation
constraint, an approach first proposed by Gobel and co-workers
{% cite gobel1994correlated %}, also in 1994. The authors' approach was
data-driven, and aimed to leverage the fact that, even in 1994, protein
sequences were easier to determine than three-dimensional structures.
Their method postulated that, since evolution holds a prime interest in
maintaining the structures of proteins, if two amino acids are in
contact in the protein chain, they will be likely to experience
correlated mutations. The authors suggested constructing a multiple
sequence alignment (MSA) from the target protein and using the
correlation between columns as a proxy for structural proximity.
Although the algorithm proposed by Gobel *et al. *was beset by biases
that precluded good performance, the idea of using multiple sequence
alignments to predict distance constraints remained.

Powered by these and other ideas, the first five editions of CASP saw
steady improvement in the free modelling category (see Figure
[1](#fig:casp14_historic)), progressing from near-random models
of all new folds in CASP1, to reasonable predictions of proteins of less
than 100 residues in CASP6 {% cite moult2005decade %}. In contrast, progress
comparatively stalled between CASP6 and CASP10
{% cite kryshtafovych2014casp10 %}, and would only resume after significant
advances in contact prediction led to notable improvement in free
modelling in CASP11 {% cite kinch2016evaluation %}. Novel statistical
algorithms, such as direct coupling analysis
{% cite weigt2009identification; @lunt2010inference; @marks2011protein %} and
inverse covariance analysis {% cite jones2012psicov %} (discussed shortly below)
were able to overcome the phylogenetic and indirect coupling biases
present in prior approaches {% cite marks2012protein %}. Powered by rapidly
growing sequence databases, more accurate predicted contacts were
incorporated to most pipelines, either contributing to the energy
function {% cite ovchinnikov2016improved %} or used directly in modelling
{% cite kosciolek2016accurate %}.

The methods described thus far were all made relatively obsolete with
the advent of AlphaFold 2 in the early 2020s
{% cite alphafold2; @jumper2021applying %}. Nevertheless, like any scientific
advance, many of the central ideas in AlphaFold 2 rely on previous
advances. In the following section, we discuss coevolutionary analysis,
one of the most powerful ideas in structure prediction.

<a name="coevolution"></a>
### 3. Exploiting sequence data

In the previous section we introduced the idea of predicting amino acids
that were in close proximity from analysis of multiple sequence
alignments. This method forms part of a family of sequence analysis
algorithms known as *coevolutionary analysis*, which underlies the
success of AlphaFold 2. We explore this concept in more detail here.

The idea behind coevolutionary analysis is that interactions between
amino acids in the sequence leave a footprint in the evolutionary
history of a protein. For example, if the structure of a protein relies
on a salt bridge between an arginine and an aspartate, a mutation on the
former will likely be accompanied by a mutation on the latter: if the
arginine is mutated to a hydrophobic amino acid, then the aspartate will
have to become hydrophobic as well. Should this not happen, the protein
would be unable to fold, "selecting out" the organism. Hence, the only
sequences present in nature are those with correlated mutations.
Coevolutionary analysis employs powerful statistical methods to extract
the causative correlations between amino acids in a protein.

The initial attempts at extracting amino acid interactions from the
multiple sequence alignment were beset by the presence of three
intrinsic biases in the data. The first one is the *entropic bias*,
which arises from limits in sampling. The second one is *phylogenetic
bias*, which refers to spurious correlations due to the evolutionary
tree of the organisms, rather than the interactions between amino acids.
The third, and most important bias is *chaining bias*, which refers to
the difference between true evolutionary covariation, or causative
correlations, and indirect correlations. For example, if residues A and
B are in contact in the structure, but also residues B and C, then there
is a chaining effect between residues A and C even though these two are
not directly interacting. Correcting these biases has been an active
area of research in structural bioinformatics.

In the early 2010s, two families of algorithms were discovered that
could correct for these biases correctly. The first family, *directed
correlation analysis* (DCA) {% cite marks2012protein %} used a global graphical
model of the protein sequence where every position was correlated with
every other position, and the parameters were estimated from the
multiple sequence alignment under strong regularization. The second
family, *inverse covariance analysis* (ICA) {% cite jones2012psicov %},
constructs a massive correlation matrix of every possible amino acid at
every position of the sequence, and estimates correlations between pairs
of variables while controlling every other variable, using the inverse
of the covariance matrix. These approaches had an enormous impact on the
quality of protein structure prediction.

Exploiting evolutionary contacts to predict protein structure has been
one of the most influential ideas in structural bioinformatics. Progress
in the field was powered by the release of multiple packages
implementing complementary statistical approaches, including FreeContact
{% cite kajan2014freecontact %}, PSICOV {% cite jones2012psicov %} and CCMPred
{% cite seemayer2014ccmpred %}, as well as metapredictors combining several
approaches, such as Meta-PSICOV {% cite jones2015metapsicov %}. Coevolution
analysis has also been used to solve problems like predicting flexible
conformations {% cite toth2016structured %}, allostery {% cite schwarz2019modeling %}
and mutation effects {% cite hopf2017mutation %}. In proteins for which
significant sequence information is not available, it is also possible
to generate artificial sequence data from deep mutational scans
{% cite rollins2019inferring; @stiffler2020protein %}.

The concept of exploiting large amount of sequence information remains
strong in modern approaches to protein structure prediction. As we will
see, both AlphaFold and AlphaFold 2 rely strongly on leveraging large
multiple sequence alignments to make predictions of protein structure.

<a name="deeplearning"></a>
### 4. Deep learning and structure prediction

The introduction of deep learning in protein structure prediction
sparked a dramatic increase in accuracy between CASP12 and CASP14. In
this section, we introduce the concept of deep learning and discuss how
it was first applied to contact prediction using coevolutionary
analysis. The next section will then discuss the architecture of
AlphaFold 2.

Deep learning is an approach to statistical learning where multiple
layers of a model are stacked sequentially, allegedly achieving an
understanding of complicated concepts by building them from simpler ones
in a hierarchical fashion {% cite Goodfellow-et-al-2016 %}. In computer vision,
for example, it has been proposed that deep learning systems can, in
their first layers, learn simple concepts like edges and corners, which
by composition can describe complex shapes like human faces
(*e.g. *Figure [2](#fig:deeplearning){reference-type="ref"
reference="fig:deeplearning"}). Deep learning approaches have for the
most part relied on layers of neural network, although the term does not
preclude other types of statistical learning techniques from being used
as building blocks (*e.g. *deep Gaussian processes {% cite damianou2013deep %}).

![**Illustration of the hierarchical nature of deep learning.** Directly
mapping an image (a collection of three $$N\times M$$ arrays with
real-valued numbers for the red, blue and green intensity at every
pixel) to its class is a complicated function. Deep learning can
surmount this problem by learning a series of nested simple mappings
which can be composed to perform powerful inferences
{% cite zeiler2014visualizing %}. This image has been reproduced from
{% cite waldrop2019news %}.](assets/images/deeplearning.png){#fig:deeplearning
width="0.9\\linewidth"}

Deep neural networks are often heavily overparametrised. For example,
AlphaFold 2 has approximately 93 million parameters {% cite alphafold2 %}, while
it has been trained on approximately 170,000 protein structures with an
average of 400-500 residues per protein. In classical statistical
learning theory, one would expect the high ratio of parameters to
training examples -- more than one parameter per residue -- to result in
overfitting, preventing any useful performance when extrapolating from
the training set. In practice, neural networks with very high
overparametrisation are still able to generalise, and it has even been
proposed that the higher the overparametrisation, the higher the
generalisation ability of the neural network
{% cite neyshabur2018towards; @nakkiran2021deep %}. The uncanny generalization
ability of deep neural networks has been the object of intense
theoretical study, with numerous proposed theories such as a tendency
towards simple functions {% cite valle2018deep %}, a bias introduced by the
stochastic gradient descent algorithms used to train them
{% cite hochreiter1997flat %}, or the flatness of typical loss function minima
{% cite soudry2018implicit %}. However, a definitive explanation to how or why
deep neural networks can generalise from data despite
overparametrisation is still lacking.

Deep neural networks proved to be much more efficient than fixed
statistical approaches at extracting coevolutionary information from
multiple sequence alignments. The first significant application of deep
learning to protein structure prediction is thought to be
RaptorX-Contact
{% cite wang2017accurate; @wang2018analysis; @pearce2021toward %}, a predictive
pipeline which used deep residual convolutional neural networks trained
on multiple sequence alignments to predict binary interresidue contacts.
The twelfth CASP assessment, when this method was presented, saw an
impressive improvement in precision for contact prediction: from 27% in
CASP11 to 47% in CASP12 {% cite schaarschmidt2018assessment %}, which also
translated into some improvement for template-free predictions
{% cite schaarschmidt2018assessment; @ovchinnikov2016improved %}. The success of
RaptorX at CASP12 showcased deep learning as a potential powerful tool
in protein structure prediction.

The next conceptual improvement was the realisation that deep neural
networks were powerful enough to remove the concept of binary contacts
(note that, traditionally, two amino acids were considered to be in
contact if their $$\beta$$-carbons, or $$\alpha$$-carbons in glycine, were
less than 8.0 Å apart), which CASP assessments had considered for over a
decade, and embrace a much powerful representation: distances. In
CASP13, three predictors, RaptorX {% cite xu2019distance %}, DMPfold
{% cite greener2019deep %} and AlphaFold {% cite senior2020improved %} used predicted
distance distributions with great success. The methods were not only
able to predict contacts with much higher precision, from 47% in CASP12
to 70% in CASP13 {% cite kryshtafovych2019critical %}, perhaps due to the higher
expressiveness of the distance prediction task, but also in terms of
template-free structure prediction where *"for the first time in CASP,
there* \[was\] *at least one model that roughly captures global topology
for all* \[targets\]*"* {% cite abriata2019further %}.

Despite the progress up to CASP13, in hindsight the overarching
methodology for template-free protein structure prediction had remained
roughly the same. Assumptions about the free energy of the protein were
still encoded in a potential, which was now derived from the probability
distributions of distances {% cite senior2020improved %}, and a structure was
determined by minimising this potential. Methods like AlphaFold and
DMPfold also allowed faster minimisation, since the distance restraints
tended to smoothen the energy surface, permitting gradient-based methods
that are much faster than fragment-based algorithms {% cite pearce2021toward %}.
However, this methodology was lacking a final conceptual breakthrough:
making the methodology *end-to-end*.

<a name="alphafold"></a>
### 5. An introduction to AlphaFold 2

![**Architectural diagram of the AlphaFold 2 protein structure
prediction system.** The protein sequence is contrasted against sequence
and structure databases, in order to produce, respectively, a multiple
sequence alignment (MSA) and a list of potential templates. This is
information is passed to the Evoformer module, which extracts
information from the MSA into the geometric representation of the
protein (and viceversa), and ultimately to the structure module, which
builds the final structure. Image modified from Jumper *et
al. *{% cite alphafold2 %}.](assets/images/af2.png){#fig:af2-architecture
width="\\linewidth"}

AlphaFold 2 represented a dramatic change in template-free protein
structure prediction: instead of following standard pipelines, where
multiple predictions were combined in a final minimization steps, the
model was trained in an *end-to-end* fashion. In deep learning parlance,
*end-to-end* means that a fully differentiable algorithm can map
directly from a given input (in protein structure prediction, a sequence
or multiple sequence alignment) to the desired output (in protein
structure prediction, the atomic coordinates of the protein). Since the
algorithm is fully differentiable, it is possible to optimise the entire
process and, assuming that a powerful enough architecture is in use, it
could *learn* the best way to map inputs to outputs, without the biases
of whatever the authors consider most appropriate. The end-to-end
methodology was one of the keys to the success of AlphaFold 2.

In 2005, when examining the progress of the structure prediction field
after a decade of CASP, John Moult rejoiced that the most successful
approaches may produce *"for proteins of less than about 100 residues*
\[\...\] *may produce one or a few approximately correct structures (4-6
Å $$\text{C}_\alpha$$-RMSD)"* {% cite moult2005decade %}. Fifteen years later,
AlphaFold 2 managed to predict most structures in CASP14 to GDTTS over
80, indicating overall agreement in both the protein backbone and the
side chains, and even in the worst cases, a structure that is either
globally consistent with the structure {% cite jumper2021applying %} or with a
dynamically similar structure
{% cite del2021alphafold2; @huang2021assessment %}. Although end-to-end deep
learning methods had been proposed previously
*e.g. *{% cite alquraishi2019end; @ingraham2018learning %}, nothing had come
even close to the success of AlphaFold 2.

The architecture of AlphaFold 2, depicted in Figure
[3](#fig:af2-architecture){reference-type="ref"
reference="fig:af2-architecture"}, is also quite different to previously
proposed models. The flow of information can be expressed as follows.
The input sequence is used to generate multiple sequence alignments and
to identify potential templates, as in most pipelines. This information
is in turn used to construct two representations. In this context, the
term *representation* or *embedding* refers to a tensor in a
high-dimensional space $$\Lambda$$ that is obtained from passing some
information through a neural network
$$f: \mathbb{R}^{n\times n} \rightarrow \Lambda$$. The neural network
depends on some parameters $$\theta$$, which are adapted iteratively
during training. Intuitively, since the model is end-to-end, given
enough data it should be able to "learn" a good function $$f(\theta)$$ to
represent the input data. These representations are thought to capture
all the information corresponding to the multiple sequence alignment and
an initial proposal of the three-dimensional structure. This information
is then transferred to the "Evoformer" module, which undertakes the
function of standard coevolution methods; and to a "structure module"
that takes in coevolution information and outputs a structure. These
modules include multiple feedback loops where the information is
recycled, leading to a powerful machine that extracts as much
information as possible from the multiple sequence analysis and projects
it all into a final prediction of the structure.

The central idea behind the Evoformer is that the information flows back
and forth throughout the network. Before AlphaFold 2, most deep learning
models would take a multiple sequence alignment and output some
inference about geometric proximity. Geometric information was therefore
a product of the network. In the Evoformer, instead, the pair
representation is a both a product and an intermediate layer. At every
cycle, the model leverages the current structural hypothesis to improve
the assessment of the multiple sequence alignment, which in turns leads
to a new structural hypothesis, and so on, and so on. Both
representations, sequence and structure, exchange information until the
network "reaches" a solid inference. This is enabled by a particular
deep neural network architecture known as *transformer*
{% cite vaswani2017attention %} which can dynamically regulate the flow of
information.

The structure module considers the protein as a "residue gas". Every
amino acid is modelled as a solid triangle, representing the three atoms
of the backbone. These triangles float around in space, and are
displaced and rotated by the network via affine matrices to form the
structure. At the beginning of the structure module, all of the residues
are placed at the origin of coordinates. At every step of the iterative
process, AlphaFold 2 produces a set of affine matrices that displace and
rotate the residues in space. One of the key ingredients is the
SE(3)-equivariant point attention (IPA). This function enables the
network to unify geometrically equivalent conformations of a protein
(*e.g. *two structures that have been displaced or rotated would be
considered to be the same one), effectively allowing the network to
"learn more" from the same amount of data. The final output of the
structure module, after three recycling iterations, is an all-atom
structure of a protein.

Some groups have also considered alternatives versions of the AlphaFold
2 architecture that may show better performance for other problems
{% cite baek2021accurate %}; or that may provide results at a much faster pace
{% cite kandathil2021ultrafast %}. Nevertheless, the performance of AlphaFold 2
has led many to declare the problem of predicting the structure of
individual protein chains essentially solved.

<a name="beyond-alphafold"></a>
### 6. Advances over AlphaFold

The AlphaFold 2 paper, alongside the code and weights of the model, was
published in *Nature* in July 2021 {% cite alphafold2 %}, sparking significant
research in structural bioinformatics. In this section, we outline some
of the current research advances over AlphaFold 2.

The performance of AlphaFold 2 is reliant on the input multiple sequence
alignments, so much work was dedicated to improve these. Multiple
sequence alignments are typically produced using hidden Markov models,
with tools like HHblits. Some groups used deep learning-based methods to
produce MSAs {% cite zhang2020deepmsa; @zheng2021protein %}, or enriched the
databases in some way. Other groups introduced additional constraints,
or generated a large number of structures and used some sophisticated
quality assessment method to identify the best models. While the special
issue of the *Proteins* journal describing the methods in CASP15 have
not yet been published, the results suggest that while there is some
improvement, especially in multimer prediction, the standard AlphaFold 2
protocol was better that about half of the submitted predictions
{% cite elofsson2022protein %}.

One of the problems with protein structure prediction methods like
AlphaFold 2 is that they require deep multiple sequence alignments which
can be mined using coevolutionary analysis. Unfortunately, this is a
problem for many proteins for which the multiple sequence alignment is
not very deep, or particular proteins, such as antibodies, where the
evolutionary history is not meaningful. One potential approach to solve
this is to substitute the multiple sequence alignment by the embedding
of a language model
{% cite chowdhury2022single; @wu2022high; @lin2022language %}, which though
slightly worse in quality than AlphaFold 2 is able to produce useful
predictions at a much faster rate. Nevertheless, it seems like the
models do not perform well for orphan proteins, which have led some
authors to suggest these language models are just memorising multiple
sequences alignments {% cite elofsson2022protein %}.

Some other methods have been developed that try to reproduce AlphaFold
2, such as OpenFold {% cite ahdritz2022openfold %} and UniFold {% cite li2022uni %}. The
OpenFold paper, in particular, offers a fascinating analysis of how
AlphaFold learns to predict protein structures, based on one technical
fact: the model can be trained to 95% accuracy in only about 5% of the
total training time (about 4-5 days on 25 GPUs), allowing the team to
experiment with multiple settings. For example, they observed that the
model could be trained on proteins containing only $$\alpha$$-helices, and
still generalise to $$\beta$$-sheets reasonably well (and vice versa).

### 7. An overview of doors opened


The ability to accurately predict the structure of proteins has had an
enormous impact on structural bioinformatics, and on biology in general.
In this section, we look at several fields where the impact of AlphaFold
2 has been truly groundbreaking.

-   **Proteome-wide structure prediction.** Concomitantly with the
    publication of AlphaFold 2, DeepMind released a database with
    predictions of full proteomes {% cite tunyasuvunakool2021highly %}. This
    database has since scaled to nearly 200 million proteins. A similar
    effort has been carried out by Facebook (now Meta) AI Research,
    which used their faster ESMfold pipeline to predict 600 million
    protein structures, including many meta-genomic proteins
    {% cite lin2022language %}. These efforts have enabled evolutionary studies
    {% cite tang2022statistical; @bordin2023alphafold2 %}, but also offered
    perspective into the limits of protein-based architecture
    {% cite akdel2022structural %}.

-   **Protein search**. Many successes in computational biology stem
    from algorithms to perform sequence searches, enabling to find
    evolutionarily and functionally related sequences. Since protein
    structure is more highly conserved than sequence, it is to be
    expected that comparing a protein's structure with other sequences
    may reveal functional features that would otherwise have been
    blurred by mutational drift. These efforts have powered multiple
    approaches to rapidly search large databases of protein structures
    {% cite van2022foldseek; @holm2022dali %}.

-   **Experimental model building**. Computational researchers tend to
    visualise experimental data as "perfect" -- nothing further from the
    truth. Whether obtained from X-ray crystallography, nuclear magnetic
    resonance, or cryoscopic electron microscopy, all structural data is
    ultimately a model with reasonable agreement with the data. In many
    cases, the information is insufficient. In X-ray crystallography,
    for example, one of the problems is that a big part of the data is
    intrinsically missing. The diffraction pattern is an image of the
    Fourier transform of the crystal structure, but due to the
    experimental setup, the phase information is lost -- one of the
    major headaches in crystallography (the phase problem). Using a
    predicted AlphaFold structure as an initial model to "guess" phase
    information has proven a very successful strategy to solve the phase
    problem {% cite chai2021alphafold; @barbarin2022x; @akdel2022structural %}.
    In cryoscopic electron microscopy, the resolution is often very low
    and it may be difficult to appreciate details close to atomic level.
    This can be remediated by combining the rough map from the
    microscopy experiment with high-resolution predictions from
    AlphaFold, which was successfully applied to model the structure of
    the nuclear pore complex {% cite fontana2022structure; @zhu2022structure %},
    a massive cellular macrostructure composed of  500 protein chains.

-   **Protein design.** One of the challenges in creating artificial
    proteins for custom functions is that it is hard to verify their
    structure. Early studies showed that AlphaFold can successfully be
    used to predict the structure of designed proteins
    {% cite moffat2021using; @jendrusch2021alphadesign %}, and it has become a
    *de facto* test to check the validity of proposed designs
    {% cite dauparas2022robust; @wicky2022hallucinating; @yeh2023novo %}.
    Alternatively, a research team from MetaAI has trained a model on 12
    million predicted AlphaFold 2 structrues with high confidence,
    finding that the model is able to find structures that -- when
    predicted with AlphaFold 2 -- fold to a given design with high
    confidence{% cite hsu2022learning %}.

-   **Downstream tasks.** Protein structures contain valuable
    information about the function and physicochemical properties of
    these molecules. Multiple papers have developed methods that
    incorporate structural features into machine learning models for
    predicting function {% cite ma2022enhancing %}, binding sites
    {% cite liu2022evaluation %}, flexibility {% cite ma2023predicting %} or
    immunogenicity {% cite shashkova2022sema; @hoie2023discotope %}, with high
    levels of success.

<a name="challenges"></a>
### 8. Open challenges

While AlphaFold 2 has had an enormous impact on research in structural
biology and bioinformatics, there are still many unsolved problems that
are at the forefront of modern research:

-   **Multiple conformations.** AlphaFold 2 has been trained to predict
    a protein crystal structure, but most proteins have *multiple*
    possible conformations {% cite lane2023protein %}. These conformations have
    functional value, for example as proteins change their activity in
    response to signals, and thus understanding them has enormous
    biological value. There is evidence that AlphaFold 2 is biased
    towards particular types of conformations, and that it tends to
    perform worse when proteins have a wider conformational ensemble
    {% cite saldano2022impact %}. Multiple methods have been proposed to predict
    multiple conformations, for example by altering the input multiple
    sequence alignment, be it by clustering {% cite wayment2022prediction %},
    subsampling {% cite del2022sampling %}, or alanine scanning
    {% cite stein2022speach_af %}. One of the main challenges with these
    approachs is to validate them, as there is limited high-quality data
    on multiple conformations, and when available, it is often biased
    towards specific protein families.

-   **Post-translational modifications**. Extrapolations from proteomics
    experiments suggest that as much as 70% of all proteins are subject
    to post-translational modifications {% cite olsen2013status %}. Since
    AlphaFold 2 only predicts proteins consisting of the 20
    proteinogenic amino acids, it can't consider the importance of
    processes like phosphorylation which have a dramatic effect on
    protein structure and conformation {% cite bagdonas2021case %}.

-   **Mutations.** The structure and behaviour of proteins can be
    significantly altered by even minute changes in conformation -- a
    single mutation is sometimes enough to make a protein misfold or
    lose its function *e.g.* in sickle-cell anemia
    {% cite nelson2008lehninger %}. Several papers have examined this fact, and
    though one recent piece of work found some correlation between small
    structural deformation upon a change in an amino acid
    {% cite mcbride2022alphafold2 %}, the overall consensus seems to be that
    AlphaFold 2 knows little about the effect of mutations
    {% cite bagdonas2021case; @buel2022can %}. Some studies have however found
    that they could use the predicted structure to estimate changes in
    the free energy of folding of a protein upon mutations
    {% cite akdel2022structural; @rhoades2022computational %}.

-   **Protein-ligand interactions.** Many proteins, particularly those
    of clinical significance, interact with ligands in some way.
    Understanding how (and how strongly) a ligand binds to some protein,
    as well as how the protein reacts to the interaction with the
    ligand, are problems with enormous importance for drug discovery and
    other biological progress. Some progress has been made in the latter
    problem, with a method to "correct" AlphaFold predictions by
    comparing them to experimental structures where we can see the
    effect of the ligands on the protein's conformation
    {% cite hekkelman2023alphafill %}. Much less progress has been made in
    predicting the conformation of a ligand in a protein, or predicting
    how strongly it is made -- a problem exacerbated by the biases in
    the datasets {% cite klarner2022bias %}.

-   **Protein-protein interactions.** Many proteins form multimers, and
    most proteins interact with another protein in some form. Predicting
    which proteins interact is useful to understand biological
    regulation networks in the cell, and elucidating the geometry of the
    interaction is useful for structure-based drug discovery of protein
    therapeutics, which are one of the most rapidly growing medicine
    classes. The same team at DeepMind that developed AlphaFold also
    produced AlphaFold-Multimer {% cite evans2021protein %}, a version that can
    predict protein complexes (and, by extension, protein-protein
    interactions). While this model outperformed every previous
    protein-protein docking tool, its accuracy lags behind the success
    at single chain prediction, to the point that it is not considered
    solved {% cite akdel2022structural %}. Nevertheless, there are specific
    subproblems in protein-protein interaction prediction that have seen
    notable progress, such as protein-peptide interactions
    {% cite johansson2022improving; @bryant2022evobind; @chang2023ranking %}.

-   **Protein-nucleic acid interactions.** Many proteins also interact
    with DNA or RNA. For example, one of the most important elements in
    gene regulation is the rate of transcription initiation, much of
    which is controlled by the binding of proteins known as
    *transcription factors* to promoter regions in DNA. Recent work has
    adapted RoseTTAFold {% cite baek2021accurate %}, a variant of AlphaFold, to
    predict protein-DNA and protein-RNA complexes {% cite baek2022accurate %}.
    While this model achieves very high average accuracy for the
    geometry, it struggles to model the interface: about half of the
    models are missing more than half of the contacts between protein
    and DNA.

-   **Protein folding.** There is a stark difference between *protein
    folding* and *protein structure prediction*, which has often been
    overlooked by popular and even scientific media. Protein structure
    prediction is the prediction of the equilibrium three-dimensional
    structure of a protein; protein folding is a description of the
    mechanism whereby an unfolded protein attains this three-dimensional
    structure {% cite dill2012protein %}. While AlphaFold 2 and derivatives
    provide a very effective solution to the former, they have not
    provided any insights on the latter. A study has looked into the
    intermediate conformations while folding, and found essentially no
    correlation with experimental data on protein folding mechanisms
    {% cite outeiral2022current %}.


<a name="conclusions"></a>
### 9. Conclusions

Protein structure prediction has been one of the most active fields of
research in computational biology. For nearly thirty years, progress in
predicting the three-dimensional structure of proteins was slow and
stalling. In the early 2020s, technical improvements that eventually
crystallised into the AlphaFold 2 architecture, led to the problem of
predicting the conformation of single chains being essentially solved.
This achievement has had a transformative effect on the field of
structural biology, and many problems -- obtaining the structure of
almost any protein domain, *a la carte* molecular replacement for
experimental model building, etc. -- that were thought to be almost
unsolvable are now commonplace.

Despite the success, there are many open challenges for structural
bioinformatics. Work in structure prediction has continued, with some
improvements over AlphaFold, although there are no major breakthroughs
in the quality of productions. Many other problems still remain, such as
predicting multiple conformations of proteins; exploring how proteins
interact with ligands, nucleic acids and other proteins; describing the
dynamic interactions of proteins; and, of course, one of the most
long-standing problems in protein science: explaining how proteins
dynamically attain their equilibrium three-dimensional structure.

<a name="bibliography"></a>
### 10. Bibliography

{% bibliography %}