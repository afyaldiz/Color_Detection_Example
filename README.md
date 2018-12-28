# BIG

import numpy as np  #matrisli ve matemat,ksel işlemler için
import cv2  #opencv görüntü işlemek için

#blue_lower = np.array([110,50,50])  #mavi aralığı
#blue_upper = np.array([120,255,255])

red_lower = np.array([0, 100, 100], np.uint8)  #kirmizi araligi
red_upper = np.array([10, 255, 255], np.uint8)


cam = cv2.VideoCapture(0)    #video baslatma



while (1):
    _, frame = cam.read()

    blur = cv2.GaussianBlur(frame, (5, 5), 0)      #gassian blur filtresi
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)   #bgrden hsvye

    red = cv2.inRange(hsv, red_lower, red_upper)   #kirmizi araligi
    #blue = cv2.inRange(hsv, lower_blue, upper_blue)

    red = cv2.erode(red, None, iterations=2)       #asindirma islemi
    red = cv2.dilate(red, None, iterations=2)      #genisleme

    #blue = cv2.erode(blue, None, iterations=2)       #asindirma islemi
    #blue = cv2.dilate(blue, None, iterations=2)      #genisleme

    sayi=0
    _, cntr, _ = cv2.findContours(red, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)    
    for i, c in enumerate(cntr):
        if cv2.contourArea(c) < 3000:   #bin pikselden buyuk algilama
            continue
        x, y, w, h = cv2.boundingRect(c) #cevreleme yapma
        cv2.rectangle(frame, (x, y), (x + w, y + h), (255, 0, 0), 0)
        isim="kirmizi"+str(sayi)
        cv2.putText(frame, isim, (x, y), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (255, 0, 0))
#cevceve cizdirip yazi yazma

        if h * w > 3000:
            sayi+=1
#kac adet oldugu
            print (sayi)
    cv2.imshow('frame', frame)
    if cv2.waitKey(1) & 0xFF == 27:
        break

cam.release()
cv2.destroyAllWindows()
