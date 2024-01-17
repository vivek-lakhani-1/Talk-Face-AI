# **talkface**: *Accurately Lip-syncing Videos In The Wild*

**Highlights**
----------
 - Weights of the visual quality disc has been updated in readme!
 - Lip-sync videos to any target speech with high accuracy
 - Sparkles: Works for any identity, voice, and language. Also works for CGI faces and synthetic voices.
 - Complete training code, inference code, and pretrained models are available..

--------
**Disclaimer**
--------
All results from this open-source code or our should only be used for research/academic/personal purposes only. As the models are trained on the <a href="http://www.robots.ox.ac.uk/~vgg/data/lip_reading/lrs2.html">LRS2 dataset</a>, any form of commercial use is strictly prohibhited. For commercial requests please contact us directly!

Prerequisites
-------------
- `Python 3.6` 
- ffmpeg: `sudo apt-get install ffmpeg`
- Install necessary packages using `pip install -r requirements.txt`.
- Face detection [pre-trained model](https://www.adrianbulat.com/downloads/python-fan/s3fd-619a316812.pth) should be downloaded to `face_detection/detection/sfd/s3fd.pth`.


Getting the weights
----------
| Model  | Description |  Link to the model | 
| :-------------: | :---------------: | :---------------: |
| talkface  | Highly accurate lip-sync | [Link](https://iiitaphyd-my.sharepoint.com/:u:/g/personal/radrabha_m_research_iiit_ac_in/Eb3LEzbfuKlJiR600lQWRxgBIY27JZg80f7V9jtMfbNDaQ?e=TBFBVW)  |
| talkface + GAN  | Slightly inferior lip-sync, but better visual quality | [Link](https://iiitaphyd-my.sharepoint.com/:u:/g/personal/radrabha_m_research_iiit_ac_in/EdjI7bZlgApMqsVoEUUXpLsBxqXbn5z8VTmoxp55YNDcIA?e=n9ljGW) |
| Expert Discriminator  | Weights of the expert discriminator | [Link](https://iiitaphyd-my.sharepoint.com/:u:/g/personal/radrabha_m_research_iiit_ac_in/EQRvmiZg-HRAjvI6zqN9eTEBP74KefynCwPWVmF57l-AYA?e=ZRPHKP) |
| Visual Quality Discriminator  | Weights of the visual disc trained in a GAN setup | [Link](https://iiitaphyd-my.sharepoint.com/:u:/g/personal/radrabha_m_research_iiit_ac_in/EQVqH88dTm1HjlK11eNba5gBbn15WMS0B0EZbDBttqrqkg?e=ic0ljo) |

Lip-syncing videos using the pre-trained models (Inference)
-------
You can lip-sync any video to any audio:
```bash
python inference.py --checkpoint_path <ckpt> --face <video.mp4> --audio <an-audio-source> 
```
The result is saved (by default) in `results/result_voice.mp4`. You can specify it as an argument,  similar to several other available options. The audio source can be any file supported by `FFMPEG` containing audio data: `*.wav`, `*.mp3` or even a video file, from which the code will automatically extract the audio.


##### LRS2 dataset folder structure

```
data_root (mvlrs_v1)
├── main, pretrain (we use only main folder in this work)
|	├── list of folders
|	│   ├── five-digit numbered video IDs ending with (.mp4)
```

Place the LRS2 filelists (train, val, test) `.txt` files in the `filelists/` folder.

##### Preprocess the dataset for fast training

```bash
python preprocess.py --data_root data_root/main --preprocessed_root lrs2_preprocessed/
```
Additional options like `batch_size` and number of GPUs to use in parallel to use can also be set.

##### Preprocessed LRS2 folder structure
```
preprocessed_root (lrs2_preprocessed)
├── list of folders
|	├── Folders with five-digit numbered video IDs
|	│   ├── *.jpg
|	│   ├── audio.wav
```

Train!
----------
There are two major steps: (i) Train the expert lip-sync discriminator, (ii) Train the talkface model(s).

<!-- ##### Training the expert discriminator -->
<!-- You can download [the pre-trained weights](#getting-the-weights) if you want to skip this step. To train it:
```bash
python color_syncnet_train.py --data_root lrs2_preprocessed/ --checkpoint_dir <folder_to_save_checkpoints>
```
##### Training the talkface models
You can either train the model without the additional visual quality disriminator (< 1 day of training) or use the discriminator (~2 days). For the former, run: 
```bash
python talkface_train.py --data_root lrs2_preprocessed/ --checkpoint_dir <folder_to_save_checkpoints> --syncnet_checkpoint_path <path_to_expert_disc_checkpoint> -->
<!-- ``` -->


Training on datasets other than LRS2
------------------------------------
Training on other datasets might require modifications to the code. Please read the following before you raise an issue:

- You might not get good results by training/fine-tuning on a few minutes of a single speaker. This is a separate research problem, to which we do not have a solution yet. Thus, we would most likely not be able to resolve your issue. 
- You must train the expert discriminator for your own dataset before training talkface.
- If it is your own dataset downloaded from the web, in most cases, needs to be sync-corrected.
- Be mindful of the FPS of the videos of your dataset. Changes to FPS would need significant code changes. 
- The expert discriminator's eval loss should go down to ~0.25 and the talkface eval sync loss should go down to ~0.2 to get good results. 

When raising an issue on this topic, please let us know that you are aware of all these points.

We have an HD model trained on a dataset allowing commercial usage. The size of the generated face will be 192 x 288 in our new model.


Acknowledgements
----------
Parts of the code structure is inspired by this [TTS repository](https://github.com/r9y9/deepvoice3_pytorch). We thank the author for this wonderful code. The code for Face Detection has been taken from the [face_alignment](https://github.com/1adrianb/face-alignment) repository. We thank the authors for releasing their code and models. We thank [zabique](https://github.com/zabique) for the tutorial collab notebook.
