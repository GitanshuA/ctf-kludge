# Description

The zip file challenge.zip contains two files output.png and corrupt_code.py. Actually the original image used to produce output.png was the image of a QR code which contains the link to the flag. The RGB values of the pixels of the original image were shifted according to fibonacci sequence to obtain the output image. The file corrupt_code.py basically contains a corrupted version of a sample code (hidden using stegsnow steganography)  as a way of telling the solver what has been done to the image.


# Solution

Below are the steps to solve the challenge:-

## Getting the corrupt code
If we try to open corrupt_code.py, we see that #Where is the code? is written.
On running output.png through exiftool, we get a comment "Who the hell added Fibonacci to this image? Anyways, Are you looking for 0112358?".
"0112358" is the passphrase for the steganography of corrupt_code.py. On decrypting, we get a file corrupt.py containing a corrupted code.

## Analysing the code

    #Still Cor upt...
     
    #So let's 'add' Fibonacci to an im ge
    # fib = {0,1, ,3 5,8,..}
    from PIL impo t Image
    imp rt numpy as np

    img = Imag .op n('image.png')
    img = np.a ray(img)
    for i in range(     :
        for j in     (width):
            img[i][j]=(img[i][j]+fib[i*(width)+j])%256
    Im ge.fromarr y(img, "RG ").save('output.png')

Looking at this code we can easily figure out that to every pixel of the image indexed from the top left corner, fibonacci numbers have been added to the R,G,B values of the pixels in sequence and then mod is taken with 256 to keep the values in valid range. For each pixel, the same fibonacci number has been added to its R,G and B values.

## Reversing the change

We can now write a code to reverse this change to the pixels. The image size is 1105X1105.

    from  PIL  import  Image
    import  numpy  as  np
     
    img  =  Image.open('input.png')
    img  =  np.array(img)
    fib  =  0
    f1  =  1
    for  i  in  range(1105):
		  for  j  in  range(1105):
			  img[i][j]=(img[i][j]-fib)%256
			  fib  = (fib+f1)%256
			  f1  = (fib-f1)%256
	Image.fromarray(img, "RGB").save('output.png')
									
*Taking this mod 256 at every step is very necessary to prevent overflow and long execution time.*

## Getting the QR Code

Once we have our final image, it just seems to be a solid color image. But on browsing different planes of the image,we can see the QR code in a bit plane (because there was just a slight difference in the background and foreground color of the QR code). 

## Getting the Flag

On scanning the QR code, we can see that it is the link to a .onion site on which the flag has been pasted. This has been done to create an initial illusion that the link is not opening.
