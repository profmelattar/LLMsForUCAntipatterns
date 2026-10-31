# LLMsForUCAntipatterns

# Replication package

**Use Case Antipatterns Revisited: An Extended Catalogue and an Evaluation of LLMs as Judges**

Mohamed El-Attar, College of Technological Innovation, Zayed University, Abu Dhabi, United Arab Emirates (Mohamed.El-Attar@zu.ac.ae)

Repository: https://github.com/profmelattar/LLMsForUCAntipatterns

The paper is under review; its DOI will be added here on publication.

This package contains everything needed to check and repeat the study:
- the antipattern catalogue;
- the generated use case models and their construction keys;
- every prompt;
- every judge reply, with run logs;
- the scripts that generated, judged and scored the models.

It also contains a configuration and run record summarizing the study's working log. Section 4.10 of the paper maps each guideline of Baltes et al. to where it is addressed.

## Reproducing the reported numbers

`score_stage1.py` and `score_stage2.py` use only the Python standard library (Python 3.9 or later). Run them from this folder:

```
python scoring/score_stage1.py
python scoring/score_stage2.py
```

`score_stage1.py` prints the numbers reported in Section 5.1 of the paper:
- unsafe and safe errors per judge and run;
- errors by antipattern;
- results for the majority of three runs and for the three-judge panel;
- Fleiss' kappa across repetitions and across judges;
- Wilson 95% confidence intervals for the problems detected.

`score_stage2.py` prints the numbers reported in Section 5.2: problems caught and false alarms per run and by majority, the three-judge panel, Fleiss' kappa across repetitions and across judges, and Wilson 95% confidence intervals for the problems caught.

`statistics_all.py` computes every statistic in Tables 6–15 of the paper and writes them to `scoring/statistics_all.json`: per-run and majority counts (including off-target false alarms), sensitivity, precision and F1 with Wilson confidence intervals, the rating distributions and Fleiss' kappa with bootstrap confidence intervals that resample whole models, Cochran's Q, exact McNemar tests with Holm correction, Fisher's exact tests, risk differences with Newcombe confidence intervals, Cohen's h, the diagnostic-run comparison, the reasoning-token analysis (Cliff's delta per response with a model-level bootstrap, and a Mann–Whitney U test on per-model means), and each judge's errors by the generator of the model. It needs `numpy`, `scipy` and `statsmodels`:

```
pip install numpy scipy statsmodels
python scoring/statistics_all.py
```

Scoring rules (Section 4.8 of the paper):
- A verdict is binary: problem or acceptable. A model or fragment with no finding counts as acceptable.
- A problem counts as caught only when the judge gives a PROBLEM verdict for the right antipattern and its `Constructs` line names every construct listed in `problem_constructs`.
- A PROBLEM verdict anywhere else is a false alarm.

## Contents

| Folder or file | What it holds | Paper section |
| --- | --- | --- |
| `00_catalogue/` | The extended use case antipattern catalogue (25 antipatterns), version 3 | 3, Appendix A |
| `01_configuration_record/` | The LLM configuration and run record: a summary of the study's working log (every LLM run with its settings and outcome, the pilots and the design decisions), as PDF and Markdown; its opening note says what it leaves out | 4 |
| `scoring/` | `parse_reply.py`, `score_stage1.py`, `score_stage2.py`, `statistics_all.py` and its output `statistics_all.json` | 4.8, 5.1–5.3 |
| `stage1/stage1_construction_key.csv` | Construction key: one row per model with its antipattern, model type, generator, domain and, for problems, the constructs a judge must name | 4.4, 4.8 |
| `stage1/1_generation/` | Generation protocol, the generation prompts used (v1.0), the full prompt development history, the generator assignment script and output, and the run sheets of the generation sittings | 4.4 |
| `stage1/2_corpus_by_slot/` | The 240 models as accepted, named by slot (antipattern, prompt, domain) | 4.4 |
| `stage1/3_models_neutral_ids/` | The same 240 models under the neutral ids M001–M240 that the judges saw | 4.4 |
| `stage1/4_judging/` | Evaluation prompt v1.0 and the three batch scripts | 4.6, 4.7, Appendix B.1 |
| `stage1/5_judge_replies/` | All 2,160 judge replies (`M###_r#.txt`), each judge's run log, batch ids and the raw batch input and output files | 5.1 |
| `stage1/6_improvements_on_problem_models.csv` | Every improvement the judges proposed on the 80 problem models, beside the catalogue's improvement for the criterion applied (rows that mark a missed problem have no improvement) | 5.4 |
| `stage1/7_ap5_diagnostic/` | The AP5-only prompt, its script, the 40 model ids and the 120 replies (a follow-up run after stage 1) | 5.1.1, Appendix B.2 |
| `stage1/prompt_trial/` | The 20-model API trial that preceded the full runs: prompt v0.2, scripts, model ids, trial key, replies and logs | 4.7 |
| `stage2/stage2_construction_key.csv` | Construction key: one row per labeled fragment (72 planted, 1 accidental problem found during verification), with the constructs a judge must name for each problem | 4.5, 4.8 |
| `stage2/stage2_neutral_key.csv` | Mapping from neutral ids N01–N24 to model ids and their three planted fragments | 4.5 |
| `stage2/1_design/` | `build_stage2.py` (rebuilds the design and generation prompts, seed 20261016), the design table, and the structural trigger lister used during verification | 4.5 |
| `stage2/2_generation/` | The 24 generation prompts, the generation script and its log | 4.5 |
| `stage2/3_models_as_generated/` | The 24 models as generated, named S2-CL-NN (Claude) and S2-CH-NN (ChatGPT) | 4.5 |
| `stage2/4_models_neutral_ids/` | The same models under the neutral ids the judges saw | 4.5 |
| `stage2/5_judging/` | The three batch scripts for stage 2 (evaluation prompt v1.0, unchanged) | 4.6, 4.7 |
| `stage2/6_judge_replies/` | All 216 judge replies, run logs, batch ids and raw batch files | 5.2 |
| `stage2/7_improvements_on_problem_fragments.csv` | Every improvement the judges proposed for the 219 correct stage 2 problem verdicts (230 statements), beside the catalogue's improvement, with the author's consistency check | 5.4 |

## Models and settings

| Role | Model id | Access |
| --- | --- | --- |
| Generator and judge | `claude-opus-5-5` | Anthropic Messages API (batch for judging); chat app for stage 1 generation |
| Generator and judge | `gpt-6-astra` | OpenAI Responses API (batch for judging); chat app for stage 1 generation |
| Judge only | `gemini-3.1-pro-preview` | Google Gemini API (batch) |

Every judge ran at its provider's default settings: no system prompt, and no temperature, reasoning or effort setting. Each judge saw each model three times, in separate calls. The run logs record the model id each provider returned, the token counts and the times.

## Notes

- **Normalization of stage 1 models.** In six ChatGPT replies, a stray closing marker line was removed, and trailing whitespace was normalized throughout. Nothing else was changed. See the configuration record, 11 October 2026.
- **Construction key and verification.** The key is the slot each model was generated for. The author verified every model against its slot by hand, without LLM help. The verification was not blind to the slot. The stage 1 corpus was screened with LLM help before that verification. In stage 2, one accidental AP22 problem (model S2-CL-03, neutral id N14) was found during verification and is scored. The paper refers to stage 2 models by their neutral ids; `stage2/stage2_neutral_key.csv` maps each to its model id and generator.
- **Paths in the scripts.** The generation and judging scripts were run on the researcher's own computer and expect API keys in environment variables. No key is included in this package. Some scripts name files by the internal names used during the study (for example `*_KEEP_PRIVATE.csv`); in this package those files are named as in the table above.
- **Prompt trial replies.** The ChatGPT and Gemini replies from the 20-model API trial are included; the Claude replies from that trial are not.

## How to cite

El-Attar, M. (2026). Replication package for "Use case antipatterns revisited: An extended catalogue and an evaluation of LLMs as judges" [Data set and scripts]. GitHub. https://github.com/profmelattar/LLMsForUCAntipatterns

## License

- Scripts (all `.py` files): MIT License.
- Everything else, including the catalogue, models, prompts, judge replies, keys and records: Creative Commons Attribution 4.0 International (CC BY 4.0).

Model replies were generated through the providers' services and are shared for research replication.
