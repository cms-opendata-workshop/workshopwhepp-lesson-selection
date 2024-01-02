---
title: "Solutions and questions"
teaching: 15
questions:
- "How should I construct selection criteria for a physics analysis?"
objectives:
- "Combine trigger, identification, and isolation information into a full selection for a specific physics process."
keypoints:
- "Triggers usually impose various kinematic restrictions on objects of interest."
- "Final state objects produced promptly from the proton collision are typically required to have significant momentum, tight ID quality, and to be isolated (depending on the topology of the physics process)."
- "Veto objects are typically selected using looser criteria so that the efficiency of the veto is very high."
- "Correlations between objects can be used either to select specifically for signal events or reject background events."
---

All of the trigger and physics object information from this lesson is combined when designing the event selection procedure for a physics analysis.

> ## Workshop analysis example: $$t\bar{t} \rightarrow (bjj)(b\ell\nu)$$
> Later in the workshop we will use a measurement of the top quark pair production cross section as an example analysis. The signal for this measurement is one top quark that decays hadronically, and one top quark that decays leptonically, to either a muon or an electron.
{: .callout}

> ## Your analysis example
> What is a physics process that you might study? Let's design a possible CMS event selection. If your process includes a particle with multiple possible decay modes, choose one (or a small group of very similar decay modes) as a test case for this challenge. 
{: .prereq}

For the $$t\bar{t}$$ measurement and/or your own process of interest, use the information you have gained about triggers and physics objects to sketch out a possible event selection for your analysis.

> ## Signal
> Which final state particles would you expect to observe in the detector from your "signal" process?
>> ## Solution
>> For the [$$t\bar{t}$$ measurement](https://link.springer.com/content/pdf/10.1007/JHEP09(2017)051.pdf) we expect one electron or muon, MET from the accompanying neutrino, and several jets, some of which could be tagged as b quark jets.
>{: .solution}
{: .objectives}


Based on these particles, consider:

 * Which trigger or triggers would be useful to make sure your signal events are represented in the dataset?

> ## Solution
> This analysis is perfect for the common "single lepton" triggers that require isolated leptons of moderate momentum in the central region of the detector. Central, isolated leptons are produced rarely enough that CMS can typically keep these triggers "unprescaled" for leptons down to about 20-30 GeV (though the threshold has needed to increase over time).
>
> Let's have a look at the trigger filter you can find in `poet_cfg.py`:
>
> ~~~
> #----------- Turn on a trigger filter by adding this module to the the final path below -------#
> process.hltHighLevel = cms.EDFilter("HLTHighLevel",
>     TriggerResultsTag = cms.InputTag("TriggerResults","","HLT"),
>     HLTPaths = cms.vstring('HLT_Ele22_eta2p1_WPLoose_Gsf_v*','HLT_IsoMu20_v*','HLT_IsoTkMu20_v*'),           # provide list of HLT paths (or patterns) you want
>     eventSetupPathsKey = cms.string(''), # not empty => use read paths from AlCaRecoTriggerBitsRcd via this key
>     andOr = cms.bool(True),             # how to deal with multiple triggers: True (OR) accept if ANY is true, False (AND) accept if ALL are true
>     throw = cms.bool(True)    # throw exception on unknown path names
> )
> ~~~
> {: .language-python}
{: .solution}


 * Which objects should you require in each event?

> ## Solution
> Certainly at least 1 muon or electron and 1 jet! Different event *categories* could be considered, depending on the number of jets or the number of b-tagged jets found in the event -- an event with a muon or electron, 2 b-tagged jets, and 2 non-b-tagged jets has a higher probability to arise from top quark pair production than most other SM processes. A MET requirement is more tricky to choose: since the neutrinos involved in this event likely do not carry away very large momenta, imposing a MET threshold may not significantly improve the selection.
{: .solution}

 * What kinematic criteria might you requier for each object? (momentum, angular regions, etc)

> ## Solution
> The trigger criterion imposes some constraints: we will lose muons with pT below 20 GeV and electrons with pT below 22 GeV or large pseudorapidity. Since there is typically some "turn-on" in the efficiency of the trigger selection, it would be safer to add a momentum buffer to our selection. This analysis requires:
> * Exactly 1 muon or electron with pT > 30 GeV and absolute $$\eta$$ < 2.1
> * At least one jet with pT > 30 GeV and absolute $$\eta$$ < 2.4. Avoiding low momentum jets reduces uncertainties due to jet energy corrections, and jets in the central region of the detector can be connected to tracks from the inner tracker for b-tagging algorithms.
> * POET branches: muon_pt, muon_eta, electron_pt, electron_eta, jet_corrpt, jet_eta 
{: .solution}

 * What quality criteria might you require for each object? (identification, isolation)

> ## Solution
> Electron selection:
>  * The trigger includes the criterion "WPLoose", which somewhat (though not always perfectly...) aligns with the "loose" working point of the identification algorithm stored in POET files. For selected electrons this analysis required they pass the "tight" working point. 
>  * We want to protect against selecting electrons that are "nonprompt", so the 3D impact parameter of the electron should lie within 4 standard deviations of the primary interaction vertex.
>  * POET branches: electron_isTight, electron_sip3d
>
> Muon selection:
>  * The ID is not restricted by the trigger, but the "tight" working point is by far the most popular choice for any signal muons.
>  * The trigger does require "Iso", so the analysis selection should also incorporate an isolation criterion. This is sensible since the muon is not expected to be produced very near to a jet. This analysis requires that the pileup-corrected particle-flow relative isolation be < 0.15.
>  * We want to protect against selecting muons that are "nonprompt", so the 3D impact parameter of the electron should lie within 4 standard deviations of the primary interaction vertex.
>  * POET branches: muon_isTight, muon_pfreliso04DBCorr, muon_sip3d
>
> Jet selection:
>  * It is standard (and applied by default in POET 2015) to apply the noise-rejection jet ID
>  * While no b-tagged jets are strictly required, the number of jets passing the "medium" working point of the CSV algorithm are counted.
>  * POET branches: jet_btag
{: .solution}

 * Are there any correlations between your objects that you might exploit?

> ## Solution
> This analysis exploits one noteworthy correlation: one b-tagged jet (if it exists) should emerge from the same top quark as the lepton. The mass of this b-tagged jet and the lepton would show a sharp drop-off below the top quark mass. If more than one b-tagged jet is found in the event, the smallest mass combination of a b-tagged jet and a lepton often yields the correct pair.
> ![](https://cds.cern.ch/record/2242828/files/Figure_002-g.png)
{: .solution}

> ## Background
> Which SM backgrounds could easily mimic your signal, given a few extra physics objects, or a few missing physics objects?
>> ## Solution
>> The dominant backgrounds for this measurement are SM processes that typically produce single-lepton final states: W boson production, and single top quark production. Due to its large cross section, multijet production can also have a large contribution, especially in categories with few b-tagged jets. Similarly, processes like Drell-Yan production that would typically lead to 2-lepton final states must be considered. 
>{: .solution}
{: .objectives}

Based on these processes, consider:
 * Which background simulations should you include in your study?

> ## Solution
> The most important sample to include is $$W \rightarrow \ell \nu$$ + jets, and single top quark production (the t-channel and tW production modes are most important). Smaller backgrounds from Drell-Yan production, $$t\bar{t} + W/Z$$, and diboson production can also be modeled from simulation. Multijet ("QCD") background simulation is often not used in final results because of the difficulty in modeling pure QCD interactions, so this background is more often estimated using data in alternate selection regions ("control regions").
{: .solution}

 * Should you apply any upper or lower limits on the numbers of certain physics objects in your events?

> ## Solution
> It is often useful to select events with *exactly* a certain number of objects rather than *at least* a certain number. Close study of background versus signal processes in simulation can help show which choices are best for a certain signal. In this case, exactly one muon or electron of the qualities described above is allowed in each event.
>
> This analysis also enhances their measurement capabilities by categorizing events based on the number of jets and the number of b-tagged jets. Categories with more jets and more b-tagged jets are more likely to contain top quark pairs. This combination of background-heavy and signal-heavy categories often improves fit results by providing more information that can constain uncertainties in the background distributions.
> ![](https://cds.cern.ch/record/2242828/files/Figure_001.png)
{: .solution}

 * Are there any objects you should *veto* from your events? What quality criteria might you choose?

> ## Solution
> Lepton veto: to protect against multi-lepton events, an event is rejected if additional leptons appear. This analysis applies a fairly strict veto, because the additional leptons are not held to the same high momentum and quality thresholds as the signal lepton:
>  * Veto-quality muon: pT > 10 GeV, absolute $$\eta$$ < 2.5, passing loose ID and isolation working points.
>  * Veto-quality electron: pT > 15 GeV, absolute $$\eta$$ < 2.5, passing loose ID and isolation working points.
{: .solution}

 * Are there any correlations between objects in background that might be useful for rejection?

> ## Solution
> Not particularly! The dominant W boson background has very similar lepton properties as top quark pairs, so this analysis relies primarily on the event categories to separate out background events. 
{: .solution}

## Applying basic event filters in POET

When running POET on AOD or MiniAOD files, it is extremely useful to apply basic event filters due to the large size of the datasets involved.  For the analysis example that we will use in the following lessons we have applied the trigger filter shown above, and a basic preliminary lepton filter, by uncommenting this `EDFilter` segment in the `poet_cfg.py` configuration file:

~~~
#---- Example of a very basic home-made filter to select only events of interest
#---- The filter can be added to the running path below if needed but is not applied by default
process.elemufilter = cms.EDFilter('SimpleEleMuFilter',
                                   electrons = cms.InputTag("slimmedElectrons"),
                                   muons = cms.InputTag("slimmedMuons"),
                                   vertices=cms.InputTag("offlineSlimmedPrimaryVertices"),
                                   mu_minpt = cms.double(26),
                                   mu_etacut = cms.double(2.1),
                                   ele_minpt = cms.double(26),
                                   ele_etacut = cms.double(2.1)
                                   )
~~~
{: .language-python}

> ## So....how would I do this my own analysis?
> The POET modules are set up to be general -- for your own analysis you would only need to edit them if you require extra information that is not already stored. In `poet_cfg.py` you could configure your own filters for triggers or other objects, to reduce the run time and file size for POET jobs.
>
> In the upcoming Analysis Example lesson you'll learn to process POET files to apply selections and draw histograms.
> In the upcoming Cloud Computing lesson you'll learn how to put this all together and scale it up.
{: .callout}


{% include links.md %}
