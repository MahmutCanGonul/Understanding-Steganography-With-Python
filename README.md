# Understanding-Steganography-With-Python
LSB Image Steganography Using Python

📗Description:

💣WHAT IS STEGANOGRAPHY?

Steganography is the science that involves communicating secret data in an appropriate multimedia carrier, e.g., image, audio, and video files. It comes under the assumption that if the feature is visible, the point of attack is evident, thus the goal here is always to conceal the very existence of the embedded data.
  
  
 💣WHAT IS LSB IMAGE STEGANOGRAPHY?

LSB Steganography is an image steganography technique in which messages are hidden inside an image by replacing each pixel’s least significant bit with the bits of the message to be hidden.
  
🏹Encode Part:

1.Step:
     
Firstly, we write the code to convert the source image into a NumPy array of pixels and store the size of the image. if image mode equal RGB n value will be 3 or if image mode RGBA n value equal 4 or is not equal any mode n value will be zero. Calculate Total pixel of image.
         
    def encode(src, message, dest):

    img = Image.open(src, 'r')
    width, height = img.size
    array = np.array(list(img.getdata()))

    if img.mode == 'RGB':
        n = 3
    elif img.mode == 'RGBA':
        n = 4
    else:
        n =0
    total_pixels = array.size//n



2.Step:

Secondly, we add a delimiter (“$t3g0") at the end of the secret message, so that when the program decodes, it knows when to stop. 

     message += "$t3g0"
     b_message = ''.join([format(ord(i), "08b") for i in message])
     req_pixels = len(b_message)

3.Step:

Thirdly, we make a check if the total pixels available is sufficient for the secret message or not.

     if req_pixels > total_pixels:
    print("ERROR: Need larger file size")

    else:
       index=0
       for p in range(total_pixels):
          for q in range(0, 3):
              if index < req_pixels:
                  array[p][q] = int(bin(array[p][q])[2:9] + b_message[index], 2)
                  index += 1

4.Step:

Finally, we have the updated pixels array and we can use this to create and save it as the destination output image.

                        array=array.reshape(height, width, n)
                        enc_img = Image.fromarray(array.astype('uint8'), img.mode)
                        enc_img.save(dest)
                        print("Image Encoded Successfully")


🏹Decode Part:

1.Step:
Firstly, we repeat a similar procedure of saving the pixels of the source image as an array, figuring out the mode, and calculating the total pixels.
    
    def Decode(src):

    img = Image.open(src, 'r')
    array = np.array(list(img.getdata()))

    if img.mode == 'RGB':
        n = 3
    elif img.mode == 'RGBA':
        n = 4

    total_pixels = array.size//n

2.Step:

Secondly, we need to extract the least significant bits from each of the pixels starting from the top-left of the image and store it in groups of 8. 

                   hidden_bits = ""
                  for p in range(total_pixels):
                        for q in range(0, 3):
                            hidden_bits += (bin(array[p][q])[2:][-1])

                   hidden_bits = [hidden_bits[i:i+8] for i in range(0, len(hidden_bits), 8)]

                    message = ""
                    for i in range(len(hidden_bits)):
                          if message[-5:] == "$t3g0":
                              break
                           else:
                              message += chr(int(hidden_bits[i], 2))
                              
                              
                              
 Last Step:
 
 Finally, we do a check if the delimiter was found or not. If not, that means there was no hidden message in the image.
 
                           if "$t3g0" in message:
                               print("Hidden Message:", message[:-5])
                           else:
                               print("No Hidden Message Found")
 


🥇Congrats!! Let's call the function:

                encode("TARGET_IMAGE_PATH","YOUR_MESSAGE","DESTINATION_IMAGE_PATH")
                decode("DESTINATION_IMAGE_PATH")
