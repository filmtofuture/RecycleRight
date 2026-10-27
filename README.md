# RecycleRight

**Point your phone at a piece of plastic and learn how to recycle it.**

RecycleRight is a free web app from [Film to Future](#about-film-to-future). It uses your phone's camera and an AI model to identify an item, then explains whether it belongs in curbside recycling, a drop-off location, or the trash, and why.

**Live site:** https://filmtofuture.github.io/RecycleRight/

---

## Why we built it

Plastic film, such as grocery bags and wrap, can't go in curbside bins, but it can be recycled at drop-off locations. Most people don't know that. When we placed a collection bin at a church, it filled with water bottles and chip bags, because telling people the rules wasn't enough. They needed to *see* the difference.

RecycleRight puts that moment in everyone's pocket: scan an item, see what it is, and learn what to do with it.

## Features

- **Live camera scan** that identifies an item in a second or two
- **10 specific categories** instead of a vague "recyclable or not"
- **Clear instructions** for each item: curbside, drop-off, or trash, with the reason
- **Types of Plastic guide** for learning more
- **Runs on your device.** Photos are never uploaded or stored
- **No install.** It works in a phone's browser
- **Photo-picker fallback** if camera access is blocked

## What it can identify

| | | | | |
|---|---|---|---|---|
| Plastic Film | PET Bottle | Plastic | Multi-layer Plastic | Styrofoam |
| Metal | Cardboard | Paper | Glass | Trash |

## How it works

1. We trained a YOLO object-detection model ([Ultralytics](https://github.com/ultralytics/ultralytics)) on a [Roboflow](https://roboflow.com) dataset built from TrashNet plus our own photos, with augmentations (rotation, brightness, blur, noise) to mimic phone camera pictures.
2. We exported the trained model to ONNX (`best.onnx`).
3. The page loads the model in the browser with [onnxruntime-web](https://onnxruntime.ai/), resizes each camera frame to 640×640 with padding, runs detection, and shows the guidance for the most confident result.

## Repository contents

```
index.html    The whole app: pages, styles, and detection code
best.onnx     The trained model
logo.png      App logo
README.md     This file
```

## Run it locally

Browsers won't load the model from a `file://` address, so use a small local server:

```bash
git clone https://github.com/YOUR-USERNAME/RecycleRight.git
cd RecycleRight
python -m http.server
```

Then open http://localhost:8000. Camera access works on `localhost` and on HTTPS sites such as GitHub Pages.

## Deploy with GitHub Pages

1. Go to **Settings → Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**.
3. Select the `main` branch and the `/ (root)` folder, then click **Save**.
4. After a minute or two, your site is live at the link shown there.

## Update the model

1. Retrain, then export:
   ```python
   from ultralytics import YOLO
   m = YOLO("path/to/best.pt")
   m.export(format="onnx", imgsz=640, opset=12, simplify=True)
   print(list(m.names.values()))   # class names, in order
   ```
2. Upload the new `best.onnx` to the repo root with the same file name, then commit.
3. If the classes changed, update `classNames` and the `INFO` result text at the top of the script in `index.html`. The class list must match the model's order exactly.

## Troubleshooting

| Problem | Likely cause |
|---|---|
| "Could not load the model" | `best.onnx` is missing, misspelled, or incomplete. It should be about 10 MB |
| Wrong labels | `classNames` doesn't match the model's class order |
| Camera blocked | Open the page in Safari or Chrome, not an app's built-in browser, and allow camera permission |
| Old version still showing | Hard-refresh or open a private tab |

Add `?debug` to the page URL to see the model's runner-up guesses and their confidence.

## Limitations

- The model is only as good as its training photos. It can misjudge unusual items, messy backgrounds, or dim light.
- It shows only the single most confident detection per photo.
- Recycling rules vary by county, so always check your local guidelines.

## Roadmap

- Detect several items in one photo
- Local recycling rules and nearby film drop-off locations by zip code
- Finer plastic types, such as HDPE and LDPE film
- A "this is wrong" button that sends photos back to improve the model
- Offline use and more languages

## About Film to Future

Film to Future is a student-led organization dedicated to raising awareness of plastic film pollution and helping people recycle it correctly.

## Team

Add your names here.

## Acknowledgments

- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) for model training
- [Roboflow](https://roboflow.com) and the TrashNet dataset for training data
- [ONNX Runtime Web](https://onnxruntime.ai/) for in-browser inference

## License

Add a license here, or remove this section. Note that Ultralytics YOLO is licensed under AGPL-3.0, so check its terms if you plan to reuse or distribute the model.
