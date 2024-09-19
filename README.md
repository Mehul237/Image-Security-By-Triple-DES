## ENCRYPTION & DECRYPTION of IMAGE through Triple DES

<br>

### Triple DES : Data encryption standard (DES)

- In cryptography, Triple DES, officially the Triple Data Encryption Algorithm, is a symmetric-key block cipher, which applies the DES cipher algorithm three times to each data block.

- Block sizes: 64 bits
- Key sizes: 168, 112 or 56 bits (keying option 1, 2, 3 respectively)

- Data encryption standard (DES) uses 56 bit key to encrypt any plain text which can be easily be cracked by using modern technologies. To prevent this from happening double DES and triple DES were introduced which are much more secured than the original DES because it uses 112 and 168 bit keys respectively. They offer much more security than DES.
 
**Triple DES:**

- Triple DES is a encryption technique which uses three instance of DES on same plain text. It uses there different types of key choosing technique in first all used keys are different and in second two keys are same and one is different and in third all keys are same.

- Triple-DES encryption uses a triple-length DATA key comprised of three 8-byte DES keys to encipher 8 bytes of data using this method:

1. Encipher the data using the first key
2. Decipher the result using the second key
3. Encipher the second result using the third key
4. The procedure is reversed to decipher data that has been triple-DES enciphered:
5. Decipher the data using the third key
6. Encipher the result using the second key
7. Decipher the second result using the first key

<hr>

![Untitled-Diagram-130-1](https://user-images.githubusercontent.com/28294942/116646664-43a4e280-a996-11eb-9624-fbac40d50855.jpg)

<hr>
<br>

## Project Working Flow:

<hr>

![New doc 30-Apr-2021 8 22 am](https://user-images.githubusercontent.com/28294942/116644568-5a950600-a991-11eb-8374-87260c38ff41.jpg)

<hr>
<br>


### Code

```py

import requests
from Crypto.Cipher import DES3
from Crypto.Random import get_random_bytes
from PIL import Image
import numpy as np

def pad(data):
    while len(data) % 8 != 0:
        data += b'\x00'
    return data

def unpad(data):
    return data.rstrip(b'\x00')

def encrypt_image(image_path, key):
    img = Image.open(image_path)
    img_data = np.array(img)
    img_bytes = img_data.tobytes()
    img_bytes = pad(img_bytes)

    cipher = DES3.new(key, DES3.MODE_ECB)
    encrypted_data = cipher.encrypt(img_bytes)

    encrypted_image = Image.frombytes(img.mode, img.size, encrypted_data)
    encrypted_image.save("encrypted_image.png")
    
    return "encrypted_image.png"

def decrypt_image(encrypted_image_path, key):
    encrypted_img = Image.open(encrypted_image_path)
    encrypted_img_data = np.array(encrypted_img)
    encrypted_img_bytes = encrypted_img_data.tobytes()

    cipher = DES3.new(key, DES3.MODE_ECB)
    decrypted_data = cipher.decrypt(encrypted_img_bytes)
    decrypted_data = unpad(decrypted_data)

    # Convert decrypted bytes back to an image
    img_size = encrypted_img.size
    decrypted_image = Image.frombytes(encrypted_img.mode, img_size, decrypted_data)
    decrypted_image.save("decrypted_image.png")
    
    return "decrypted_image.png"

def download_image(url, output_path):
    file_id = url.split('/d/')[1].split('/')[0]
    download_url = f"https://drive.google.com/uc?id={file_id}"
    
    response = requests.get(download_url)
    
    if response.status_code == 200:
        with open(output_path, 'wb') as f:
            f.write(response.content)
        print(f"Image downloaded to {output_path}")
    else:
        print("Failed to download the image.")

def main():
    # Download the image
    image_url = "https://drive.google.com/file/d/1J8JQ10h_c37YIYpyLZ59J-jIGPsqZ9nY/view?usp=sharing"
    output_image_path = "downloaded_image.png"
    download_image(image_url, output_image_path)

    # Generate a random 24-byte key for Triple DES
    key = get_random_bytes(24)

    # Encrypt the downloaded image
    encrypted_image_path = encrypt_image(output_image_path, key)
    print(f"Encrypted image saved as: {encrypted_image_path}")

    # Decrypt the image
    decrypted_image_path = decrypt_image(encrypted_image_path, key)
    print(f"Decrypted image saved as: {decrypted_image_path}")

if __name__ == "__main__":
    main()

```

<br>
<hr>

![Screenshot 2024-09-19 132433](https://github.com/user-attachments/assets/2e9c44e4-6f2f-40e3-8d86-1378c0d386b9)

<hr>
<br>

### Output in Directory folder

<br>
<hr>

![Screenshot 2024-09-19 132544](https://github.com/user-attachments/assets/ceab8cc1-c588-4008-8b14-6dec9f73f2dd)

<hr>
<br>
<br>

## HTML

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image Encryptor/Decryptor</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Image Encryptor/Decryptor</h1>
        <input type="file" id="imageInput" accept="image/*">
        <button id="encryptBtn">Encrypt Image</button>
        <button id="decryptBtn">Decrypt Image</button>
        
        <h2>Output</h2>
        <canvas id="outputCanvas" style="display:none;"></canvas>
        <img id="outputImage" alt="Output" />
    </div>
    
    <script src="script.js"></script>
</body>
</html>

```

<br>

## CSS

```css

body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    text-align: center;
}

.container {
    margin: 20px auto;
    padding: 20px;
    border: 2px solid #ccc;
    background-color: white;
    border-radius: 8px;
    width: 400px;
}

button {
    margin: 10px;
    padding: 10px 20px;
    cursor: pointer;
    border: none;
    background-color: #28a745;
    color: white;
    border-radius: 5px;
}

button:hover {
    background-color: #218838;
}

img {
    margin-top: 20px;
    max-width: 100%;
}

```

<br>

## JavaScript

```js

document.getElementById('imageInput').addEventListener('change', handleImageUpload);
document.getElementById('encryptBtn').addEventListener('click', encryptImage);
document.getElementById('decryptBtn').addEventListener('click', decryptImage);

let uploadedImage = null;
let encryptedData = null;

function handleImageUpload(event) {
    const file = event.target.files[0];
    const reader = new FileReader();
    
    reader.onload = function(e) {
        uploadedImage = e.target.result;
        const img = new Image();
        img.onload = function() {
            const canvas = document.getElementById('outputCanvas');
            canvas.width = img.width;
            canvas.height = img.height;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(img, 0, 0);
            document.getElementById('outputImage').src = canvas.toDataURL();
        }
        img.src = uploadedImage;
    }
    
    if (file) {
        reader.readAsDataURL(file);
    }
}

function encryptImage() {
    if (!uploadedImage) return;
    
    // Simple encryption: reverse the image data
    const canvas = document.getElementById('outputCanvas');
    const ctx = canvas.getContext('2d');
    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
    
    for (let i = 0; i < imageData.data.length; i += 4) {
        imageData.data[i] = 255 - imageData.data[i];     // Invert Red
        imageData.data[i + 1] = 255 - imageData.data[i + 1]; // Invert Green
        imageData.data[i + 2] = 255 - imageData.data[i + 2]; // Invert Blue
    }
    
    ctx.putImageData(imageData, 0, 0);
    encryptedData = canvas.toDataURL();
    document.getElementById('outputImage').src = encryptedData;
}

function decryptImage() {
    if (!encryptedData) return;

    const img = new Image();
    img.onload = function() {
        const canvas = document.getElementById('outputCanvas');
        canvas.width = img.width;
        canvas.height = img.height;
        const ctx = canvas.getContext('2d');
        ctx.drawImage(img, 0, 0);
        
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        
        for (let i = 0; i < imageData.data.length; i += 4) {
            imageData.data[i] = 255 - imageData.data[i];     // Invert Red
            imageData.data[i + 1] = 255 - imageData.data[i + 1]; // Invert Green
            imageData.data[i + 2] = 255 - imageData.data[i + 2]; // Invert Blue
        }
        
        ctx.putImageData(imageData, 0, 0);
        document.getElementById('outputImage').src = canvas.toDataURL();
    }
    
    img.src = encryptedData;
}


```
