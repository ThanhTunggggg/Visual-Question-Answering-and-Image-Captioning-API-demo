# Pretrained Models

Performing inference using pretrained models in Pythia is easy. Pickup a pretrained
model from the table below and follow the steps to do inference or generate
predictions for EvalAI evaluation. This section expects that you have already installed the
required data as explained in [quickstart](./quickstart).

```eval_rst
+--------+-----------+-----------------------+---------------------------------------------------+-----------------------------------------------------------+
| Model  | Model Key | Supported Datasets    | Pretrained Models                                 | Notes                                                     |
+--------+-----------+-----------------------+---------------------------------------------------+-----------------------------------------------------------+
| Pythia | pythia    | vqa2, vizwiz, textvqa | `vqa2 train+val`_, `vqa2 train only`_,  `vizwiz`_ | VizWiz model has been pretrained on VQAv2 and transferred |
+--------+-----------+-----------------------+---------------------------------------------------+-----------------------------------------------------------+
| LoRRA  | lorra     | vqa2, vizwiz, textvqa | `textvqa`_                                        |                                                           |
+--------+-----------+-----------------------+---------------------------------------------------+-----------------------------------------------------------+
| BAN    | ban       | vqa2, vizwiz, textvqa | Coming soon!                                      | Support is preliminary and haven't been tested throughly. |
+--------+-----------+-----------------------+---------------------------------------------------+-----------------------------------------------------------+
| BUTD   | butd      | coco                  | `coco`_                                           |                                                           |
+--------+-----------+-----------------------+---------------------------------------------------+-----------------------------------------------------------+

.. _vqa2 train+val: https://dl.fbaipublicfiles.com/pythia/pretrained_models/vqa2/pythia_train_val.pth
.. _vqa2 train only: https://dl.fbaipublicfiles.com/pythia/pretrained_models/vqa2/pythia.pth
.. _vizwiz: https://dl.fbaipublicfiles.com/pythia/pretrained_models/vizwiz/pythia_pretrained_vqa2.pth
.. _textvqa: https://dl.fbaipublicfiles.com/pythia/pretrained_models/textvqa/lorra_best.pth
.. _coco: https://dl.fbaipublicfiles.com/pythia/pretrained_models/coco_captions/butd.pth
```

Now, let's say your link to pretrained model `model` is `link` (select from table > right click > copy link address), the respective config should be at
`configs/[task]/[dataset]/[model].yml`. For example, config file for `vqa2 train_and_val` should be
`configs/vqa/vqa2/pythia_train_and_val.yml`. Now to run inference for EvalAI, run the following command.

```
cd ~/pythia/data
mkdir -p models && cd models;
# Download the pretrained model
wget [link]
cd ../..;
python tools/run.py --tasks [task] --datasets [dataset] --model [model] --config [config] \
--run_type inference --evalai_inference 1 --resume_file data/[model].pth
```

If you want to train or evaluate on val, change the `run_type` to `train` or `val`
accordingly. You can also use multiple run types, for e.g. to do training, inference on
val as well as test you can set `--run_type` to `train+val+inference`.

if you remove `--evalai_inference` argument, Pythia will perform inference and provide results
directly on the dataset. Do note that this is not possible in case of test sets as we
don't have answers/targets for them. So, this can be useful for performing inference
on val set locally.

Table below shows evaluation metrics for various pretrained models:

```eval_rst
+--------+--------------------+---------------------------------+----------------------------------------------+
| Model  | Dataset            | Metric                          | Notes                                        |
+--------+--------------------+---------------------------------+----------------------------------------------+
| Pythia | vqa2 (train+val)   | test-dev accuracy - 68.31%      | Can be easily pushed to 69.2%                |
+--------+--------------------+---------------------------------+----------------------------------------------+
| Pythia | vqa2 (train)       | test-dev accuracy - 66.7%       |                                              |
+--------+--------------------+---------------------------------+----------------------------------------------+
| Pythia | vizwiz (train)     | test-dev accuracy - 54.22%      | Pretrained on VQA2 and transferred to VizWiz |
+--------+--------------------+---------------------------------+----------------------------------------------+
| LoRRA  | textvqa (train)    | val accuracy - 27.4%            |                                              |
+--------+--------------------+---------------------------------+----------------------------------------------+
|        |  coco              | | BLEU 1 - 76.02, BLEU 4- 35.42 |                                              |
| BUTD   |  (karpathy-train)  | | METEOR- 27.39, ROUGE_L- 56.17 | Beam Search(length 5), Karpathy test split   |
|        |                    | | CIDEr - 112.03, SPICE - 20.33 |                                              |
+--------+--------------------+---------------------------------+----------------------------------------------+
```

**Note for BUTD model :**  For training BUTD model use the config `butd.yml`. Training uses greedy decoding for validation. Currently we do not have support to train the model using beam search decoding validation. We will add that support soon. For inference only use `butd_beam_search.yml` config that supports beam search decoding.

