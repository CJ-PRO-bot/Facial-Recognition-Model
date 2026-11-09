# Facial Expression Classifier — local upload app

This folder contains a working image-upload Gradio application built around **your
already-trained AffectNet CNN**. It classifies an uploaded face photo as **Angry,
Happy, or Sad**. It does **not** train a new model, require a Kaggle token, use
Google Drive, or upload pictures to a hosted inference API.

## What is included

- `app.py` — ready-to-run local application
- `config.json` — your saved Keras CNN architecture (96×96 RGB input)
- `model.weights.h5` — your trained model weights
- `metadata.json` — original Keras export metadata
- `requirements.txt` — interface and image libraries
- `Launch_App.ipynb` — launch directly from Jupyter Notebook

## Run from Jupyter Notebook (recommended)

1. Extract the ZIP so these files remain together.
2. Open `Launch_App.ipynb` from that same folder in **Jupyter Notebook**.
3. Choose the kernel/environment where your TensorFlow/Jupyter setup works.
4. Run the installation cell only if necessary, then run the launch cell.
5. Open the local link, usually `http://127.0.0.1:7860/`.
6. Upload a JPG/PNG image; classification happens automatically. You can also
   click **Analyze photo**.

## Run from Windows Terminal or Anaconda Prompt

Navigate into the unzipped folder and run:

```bash
python -m pip install -r requirements.txt
python app.py
```

**Important:** Keras 3 needs an inference backend. If TensorFlow is already
installed in your selected Jupyter Python environment, you do not have to
install it again. If neither TensorFlow nor PyTorch is installed, install
TensorFlow separately (`python -m pip install tensorflow`) or select a Python
environment where one backend is already available. If you have both installed,
the app uses TensorFlow by default; to force the PyTorch backend, set the
`KERAS_BACKEND=torch` environment variable before starting Python.

You will see the page at `http://127.0.0.1:7860/`.

## How it works

1. Photo uploads are processed **on your own machine**, with optional resizing
   of large images to reduce CPU/memory use.
2. The app tries to find the largest front-facing face using OpenCV's bundled
   Haar-cascade detector and crops it with a 10% margin.
3. It resizes the crop to **96 × 96 RGB**, with pixel values **0–255**. Do **not**
   divide by 255 yourself: the saved CNN already includes `Rescaling(1/255)`.
4. The model predicts in the original trained output order:
   **0 Angry, 1 Happy, 2 Sad**.
5. All three scores are shown. If the best score is below **55%**, the display
   indicates **Uncertain**.

If a portrait is already tightly cropped and the face detector cannot find it,
select **Use entire image as a cropped face**. Only choose this for images that
really contain a face. No model can reliably classify arbitrary nonface images.

## Limitations

- A classifier can estimate a **visible facial expression**, not reliably know
  the person's internal emotion or mental state.
- The 55% threshold is a UI convention, not an independently calibrated
  confidence threshold.
- For photos with multiple people, only the largest detected face is processed.
- Detection favors frontal faces; extreme angles/occlusion can fail.
- The underlying notebook reports a *test macro-F1 of 0.8309* for the selected
  AffectNet CNN. This is **reported notebook performance**, not a claim that
  the model achieves 83.09% accuracy on arbitrary uploaded photos.

## Troubleshooting

- `ModuleNotFoundError: gradio`: install with the selected kernel's Python,
  e.g. `python -m pip install gradio`.
- `No Keras backend found`: use the Python environment where TensorFlow was
  installed for training, or install TensorFlow / PyTorch separately.
- `config.json or model.weights.h5 missing`: keep both files beside `app.py`.
- `No face detected`: try good lighting, front-facing portrait, or the crop
  checkbox for a tightly cropped face.
- `Address already in use`: close any previous running app on port 7860.
