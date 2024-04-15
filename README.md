# Diarizers

Diarizers is a library for fine-tuning speaker diarisation models from [`pyannote`](https://github.com/pyannote/pyannote-audio/tree/develop) using hugging face tools.It provides scripts to: 

- Convert Speaker Diarization datasets into Hugging Face datasets compatible with `diarizers`. 
- Train from scratch or fine-tune speaker diarization models using the Hugging Face `Trainer`. 
- Evaluate the models at inference time. 
-  Convert the fine-tuned models into a format compatible with `pyannote`.  

> [!IMPORTANT]
> This library can only be used to fine-tune the [segmentation model](https://huggingface.co/pyannote/segmentation-3.0) from the `pyannote` [speaker diarization pipeline](https://huggingface.co/pyannote/speaker-diarization-3.1) for now. 
> Future work will aim to help optimise the full pipeline hyperparameters. 

## 📖 Quick Index
* [Installation](#installation)
* [Adding new datasets](#datasets)
* [Training](#training)
* [Inference](#inference)
* [Acknowledgements](#acknowledgements)
* [Citation](#citation)
* [Contribution](#contribution)

## Installation

Diarizers has light-weight dependencies and can be installed with the following lines:

```sh
pip install git+https://github.com/huggingface/diarizers.git
pip install diarizers[dev]
```

## Datasets

Features: 


- `audio`:
- `timestamps_start`:
- `timestamps_end`:
- `speakers`:



## Fine-Tune: 

```
python3 train_segmentation.py
    --dataset_name="kamilakesbi/real_ami_ihm" \
    --from_pretrained=True \
    --lr='1e-3'\
    --batch_size='32'\
    --epochs='3'\
    --do_init_eval=True\
    --checkpoint_path='checkpoints/ami'\
    --save_model=True \
    --num_proc='12'
```

## Test: 

```
python3 test_segmentation.py \
   --dataset_name="kamilakesbi/real_ami_ihm" \
   --pretrained_or_finetuned='finetuned' \
   --checkpoint_path='checkpoints/ami'
```


## Use in pyannote: 

- Use the fine-tuned segmentation model for inference: 

```python
from diarizers.models.segmentation.hf_model import SegmentationModel
from pyannote.audio import Inference

input_audio = {'waveform': waveform, 'sample_rate': sample_rate}

model = SegmentationModel().from_pretrained('checkpoints/ami')
model = model.to_pyannote_model()

# Inference result: 
segmentation_output = Inference(model, step=2.5)(input_audio)
```

- Use the fine-tuned segmentation model in a speaker diarization pipeline: 


```python

from diarizers.models.segmentation.hf_model import SegmentationModel
from pyannote.audio import Pipeline

input_audio = {'waveform': waveform, 'sample_rate': sample_rate}

pipeline = Pipeline.from_pretrained(
            "pyannote/speaker-diarization-3.1",
        )

# replace the segmentation model with yours: 
model = SegmentationModel().from_pretrained('checkpoints/ami')
model = model.to_pyannote_model()
pipeline.segmentation_model = model


diarization_output = pipeline(input_audio)

# dump the diarization output to disk using RTTM format
with open("audio.rttm", "w") as rttm:
    diarization.write_rttm(rttm)
```

## Acknowledgements



## Citation

If you found this repository useful, please consider citing this work and also the original `pyannote` paper:

```
@misc{akesbi-diarizers,
  author = {Kamil Akesbi and Sanchit Gandhi},
  title = {"Diarizers: A repository for fine-tuning speaker diarization models"},
  year = {2024},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/huggingface/diarizers}}
}
```

```
@inproceedings{bredin-pyannote,
  author={Hervé Bredin},
  title={"pyannote.audio 2.1 speaker diarization pipeline: principle, benchmark, and recipe"},
  year={2023},
  booktitle={Proc. INTERSPEECH 2023},
}
```

## Contribution
