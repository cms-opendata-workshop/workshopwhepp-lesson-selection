---
title: "Introduction"
teaching: 10
exercises: 0
questions:
- What did we learn in the physics objects pre-learning module?
- How should I select events for a physics analysis?
objectives:
- Summarize information available from POET for various physics objects.
- Describe the fundamental elements of event selection in CMS.
keypoints:
- POET output files contain the important kinematics, identification, isolation, and tagging information typically needed for analysis event selection.
- Event selection criteria must be a reasoned balance of physics objects to keep, physics objects to reject, and trigger options from CMS.
---

## What we've learned so far...

The **CMS experment** is a giant detector that acts like a camera that "photographs" particle collisions, allowing us to interpret their nature. In the [Physics Objects pre-learning exercise](https://cms-opendata-workshop.github.io/workshop2023-lesson-physics-objects/), you read about the following physics objects reconstructed by CMS:

* muons
* electrons
* photons
* taus
* jets
* missing transverse momentum

The [Physics Object Extractor Tool](https://github.com/cms-opendata-analyses/PhysObjectExtractorTool/tree/2015MiniAOD) is a collection of EDAnalyzer modules that can be used to process CMS MiniAOD files and extract information about the different physics objects. 

## Summary of POET contents

POET files are full of information! When you open a POET file you'll see many "trees" in the `myoutput.root` that correspond to the results of different EDAnalyzer modules. The trees are ["Friends"](https://root.cern/manual/trees/#widening-a-ttree-through-friends) 
in the sense that they contain the same collision or simulation events in the same order, but they each contain different sets of information about the event.

 * `mymuons`: 4-vector information, charge, identification working point pass/fail, impact parameters, isolation metrics.
 * `myelectrons`: 4-vector information, charge, combined identification + isolation working point pass/fail, impact parameters.
 * `myphotons`: 4-vector information, isolation metrics, identification working point pass/fail
 * `mytaus`: 4-vector information, hadronic tau identification working point pass/fail
 * Jets:
    * `myjets`: angular 4-vector information, momentum/energy/mass 4-vector information in uncorrected and corrected forms, b-tagging information, uncertainties related to jet energy corrections and b-tagging corrections (for simulation).
    * `myfatjets`: angular 4-vector information, momentum/energy/mass 4-vector information in uncorrected and corrected forms, "groomed" jet mass options, N-subjettiness variables, subjet b-tagging information.
 * `mymets`: full-event transverse energy sums (scalar `SumEt`, vector `MET`) and azimuthal angle in uncorrected and corrected forms, MET significance metric.

## Event Selection principles

When beginning a CMS analysis, there are three guiding principles to consider. Let's assume you have a Feynman diagram in your head representing some physics process that you would like to measure or search for.

> ## Guiding principles
> 1. What physics objects should be present to represent the final state particles of my Feynman diagram? Should any of the objects be related to each other in a special way?
> 2. What physics objects should NOT be present?
> 3. What will cue CMS to store the types of events I want to analyze?
{: .testimonial}

### Choosing things to keep

No physics object in CMS is reconstructed with absolute certainty. We always need to consider whether a reconstructed object is "genunine" or "fake", and the pre-computed identification algorithms are designed to help
analysts avoid considering "fake" objects that were caused by spurious information such as detector noise. In fact, for jets, the noise-rejection identification algorithm is applied in POET universally for 2015!

Other considerations are whether objects are "prompt" or "nonprompt" (or "displaced"): muons from a Higgs boson 4-muon decay would be considered "prompt"; muons emerging from b-hadron decays within a jet would be considered "nonprompt"; and muons emerging far from the interaction point from the decay of some long-lived particle would be considered "displaced". Identification and isolation algorithms can piece these differences apart, but each analysis will apply different choices. 

Jets carry information about the quark or boson that produced them, which is described as "tagging" in CMS. Analysts can choose to implement a jet tagging algorithm to select out jets with certain features. 

### Choosing things to drop

All measurements and searches must consider background processes: reducible backgrounds with different final states that may pass event selection criteria due to some mismeasurement or fluctuation, and irreducible backgrounds with the same final state physics objects. Clever selection choices can often drop the rate of background processes significantly without sacrificing too many signal events. One basic example is the practice
of using high momentum thresholds in searches for massive new physics particles, since SM processes with the same final state will preferentially result in low-momentum physics objects. Any physics object that can be *selected* can also be *vetoed*, depending on the needs of the analysis. An important part of this process is identifying and studying SM background processes!

### Choosing a set of triggers

Triggers determine which collision events are kept or discarded by CMS, so it sounds like this criterion should be chosen first, but in practice it is typically chosen last. Armed with a set of physics object selection criteria, we can search for a "trigger" or set of triggers that should have passed any event that will also pass the analysis criteria. More on this next!

{% include links.md %}
