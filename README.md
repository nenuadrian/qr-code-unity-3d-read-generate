# qr-code-unity-3d-read-generate
Generating a QR code / Scanning a QR code in Unity 3D

At least at the time when I needed this feature, there were barely any usable tutorials or QR libraries ready to use for Unity (given the older .net framework it uses).

Took me a few days of trial and error until finding and properly building the ZXing library DLL which you can find in the repo.

# Read a QR
```
using ZXing;
using ZXing.QrCode;

private WebCamTexture camTexture;
private Rect screenRect;
void Start() {
  screenRect = new Rect(0, 0, Screen.width, Screen.height);
  camTexture = new WebCamTexture();
  camTexture.requestedHeight = Screen.height;
  camTexture.requestedWidth = Screen.width;
  if (camTexture != null) {
    camTexture.Play();
  }
}

void OnGUI () {
  // drawing the camera on screen
  GUI.DrawTexture (screenRect, camTexture, ScaleMode.ScaleToFit);
  // do the reading — you might want to attempt to read less often than you draw on the screen for performance sake
  try {
    IBarcodeReader barcodeReader = new BarcodeReader ();
    // decode the current frame
    var result = barcodeReader.Decode(camTexture.GetPixels32(), camTexture.width , camTexture.height);
    if (result != null) {
      Debug.Log(“DECODED TEXT FROM QR: “ + result.Text);
    }
  } catch(Exception ex) { Debug.LogWarning (ex.Message); }
}
```


# Generate one
```
private static Color32[] Encode(string textForEncoding, int width, int height) {
  var writer = new BarcodeWriter {
    Format = BarcodeFormat.QR_CODE,
    Options = new QrCodeEncodingOptions {
      Height = height,
      Width = width
    }
  };
  return writer.Write(textForEncoding);
}

public Texture2D generateQR(string text) {
  var encoded = new Texture2D (256, 256);
  var color32 = Encode(text, encoded.width, encoded.height);
  encoded.SetPixels32(color32);
  encoded.Apply();
  return encoded;
}

Texture2D myQR = generateQR("test");
if (GUI.Button (new Rect (300, 300, 256, 256), myQR, GUIStyle.none)) {}
```


I am using Unity 5.3.5
