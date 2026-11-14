Tak 2

1. Screenshot of the live classification was taken and saved as phone_screenshot.jpg.

2. I uploaded check_image.jpg to my web page and saved a screenshot of the result as phone_screenshot_check_picture.jpg. That confirming the predicted probabilities agree with check_expected.json to at least three decimal places (apple: 100,00% x 0,999983; cherry: 1,68e-5 x 1,68e-5; chestnut: 1,06e-8 x 1,06e-8; and maple: 2,34e-10 x 2,34e-10), with only minor display rounding in the percentage output.

3.In index.htlm the frameToTensor() function crops a central square. Math.min(srcW, srcH) and resizes it to 96x96. Using drawImage() converts the RGB values obtained from gelImageData() into range 0-1 by dividing 255. Standardizes each channel using the mean and std values from model_info.json, and stores the data in a Float32Array tensor with an NCHW format of [1, 3, 96, 96], which matches model_info.json and the notebook so nothing needed to be fixed.

4.Neither camera feeds nor uploaded photos are sent anywhere. All processing and inference using ort.InferenceSession with WASM take place locally within the phone's browser. The page only downloads the onnxruntime-web library from a CDN cdn.jsdelivr.net and the local files model_info.json and leaf_model.onnx from GitHub Pages. The QR code is rendered locally as an inline SVG.