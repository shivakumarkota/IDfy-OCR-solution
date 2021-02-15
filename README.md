License Plate Recognition(LPR) with OpenCV and Tesseract OCR


Python-tesseract: 
Py-tesseract is an optical character recognition (OCR) tool for python. That is, it’ll recognize and “read” the text embedded in images. 
Python-tesseract is a wrapper for Google’s Tesseract-OCR Engine.  It is also used as an individual script, 
because it can read all image types like jpeg, png, gif, bmp, tiff, etc.

OpenCV: 
OpenCV is an open source computer vision library. The library has more than 2500 optimized algorithms.
These algorithms are often used to search and recognize faces, identify objects, recognize scenery and generate markers to overlay images using augmented reality, etc.

# install required libraries

# Loading the required python modules
import pytesseract # this is tesseract module
import cv2 # this is opencv module
import glob
import re
import pandas as pd
from collections import Counter

#####################Perform OCR using the Tesseract Engine on license plates
#loading the tesseract file
pytesseract.pytesseract.tesseract_cmd = r'Tesseract-OCR\\tesseract.exe'

annotations = pd.read_csv('annot.csv')
annotations_list = list(annotations['Annot'])

#####################specify path to the license plate images folder as shown below######################
path_for_license_plates = 'License_plate\\**\\*.png'
predicted_license_plates = []

####################reading the license plate image files############################
for path_to_license_plate in glob.glob(path_for_license_plates):

    img = cv2.imread(path_to_license_plate)
    predicted_result = pytesseract.image_to_string(img, lang='eng', config='--psm 9 --oem 1 -c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789')
    predicted_result = predicted_result.replace('\n\x0c','')
    regex = r"\[|\]|\||\_|\-|\)|\(|\"|\"|\“|\“|\:|\,|\.|[a-z]"
    sub = ''
    predicted_result = re.sub(regex,sub,str(predicted_result))

    #################### cleaning/trimming process#########################
    if len(predicted_result.split(' '))>1:
        sb_st = predicted_result.split(' ')
        if len(sb_st[0])>3:
            step = len(sb_st[0])-3
            temp1 = sb_st[0][step:]
        else:
            temp1 = sb_st[0]
        if len(sb_st[1])>4:
            step = len(sb_st[1])-4
            temp2 = sb_st[1][:-step]
        else:
            temp2 = sb_st[1]
        predicted_result = temp1+' '+temp2

    regex1 = r"\s"
    predicted_result = re.sub(regex1, sub, str(predicted_result))
    # print(predicted_result)
    predicted_license_plates.append(predicted_result)


#######the plates predicted but haven’t seen what is the prediction, so to view the data and prediction do a bit of visualization as shown below.
and  also calculating the accuracy of prediction without using any built-in function##################################

def calculate_predicted_accuracy(actual_list, predicted_list):
    acc = []
    for actual_plate, predict_plate in zip(actual_list, predicted_list):
        accuracy = "0 %"
        num_matches = 0
        if actual_plate == predict_plate:
            accuracy = "100 %"
        else:
            if len(actual_plate) == len(predict_plate):
                for a, p in zip(actual_plate, predict_plate):
                    if a == p:
                        num_matches += 1
                accuracy = str(round((num_matches / len(actual_plate)), 2) * 100)
                accuracy += "%"
        acc.append(accuracy)
        print("	 ", actual_plate, "\t\t\t", predict_plate, "\t\t ", accuracy)
    return acc


print("Actual License Plate", "\t", "Predicted License Plate", "\t", "Accuracy")
print("--------------------", "\t", "-----------------------", "\t", "--------")

acc = calculate_predicted_accuracy(annotations_list, predicted_license_plates)

Counter(acc)


Output:

Actual License Plate 	 Predicted License Plate 	 Accuracy
-------------------- 	 ----------------------- 	 --------
	  9B52145 			 9852145 		  86.0%
	  6B94558 			 6B94558 		  100 %
	  8B90164 			 8B90164 		  100 %
	  5B11181 			 61U 		  0 %
	  8B79697 			 0879697 		  71.0%
	  8B90164 			 8B90164 		  100 %
	  5B11181 			 511184 		  0 %
	  5Z40181 			 510181 		  0 %
	  2B20174 			 2820174 		  86.0%
	  1B76462 			 1B76462 		  100 %
	  BVJ6411 			 BVJ6411 		  100 %
	  8B79697 			 887 		  0 %
	  BVJ6411 			 BVI6411 		  86.0%
	  1B94686 			 1B4686 		  0 %
	  4B83940 			 4B83940 		  100 %
	  4B83940 			 4B83940 		  100 %
	  4B27433 			 4B27433 		  100 %
	  5B29737 			 5B29737 		  100 %
	  8B88391 			 8B88394 		  86.0%
	  5B28893 			 68 		  0 %
	  5C48306 			 M56306 		  0 %
	  3B38140 			 W338140 		  71.0%
	  4B10036 			 481003 		  0 %
	  1BD0673 			  		  0 %
	  4B50811 			  		  0 %
	  6B51255 			 AP65 		  0 %
	  4B02466 			 OES 		  0 %
	  5C65578 			 3 		  0 %
	  BZD1223 			 L1223 		  0 %
	  ZNE2393 			 2390 		  0 %
	  8B02173 			 8B02173 		  100 %
	  7B10307 			  		  0 %
	  9B60464 			 464 		  0 %
	  7B91102 			 7831102 		  71.0%
	  1AT5828 			 T2 		  0 %
	  7B14349 			 7814343 		  71.0%
	  4M98681 			 LAS8681 		  56.99999999999999%
	  2B17515 			 2B17515 		  100 %
	  BZH8389 			 52H8369 		  56.99999999999999%
	  9B38368 			 9 		  0 %
	  8B71401 			 BB/= 		  0 %
	  4B20737 			 4B20737 		  100 %
	  3B04082 			 3804082 		  86.0%
	  1J24813 			 1J2 		  0 %
	  1BC4146 			 BC4146 		  0 %
	  5B77655 			 5877665 		  71.0%
	  6B67495 			 6B67495 		  100 %
	  BZP2087 			 BZP2087 		  100 %
	  5C93988 			 59°3988 		  71.0%
	  2B65055 			 2855055 		  71.0%
	  3AI0851 			 ON0851 		  0 %
	  2SE8601 			  		  0 %
	  7B11859 			 7511859 		  86.0%
	  3AI0851 			 SATS085 		  14.000000000000002%
	  8B23170 			 B823170 		  71.0%
	  9B84391 			 9B84391 		  100 %
	  2B13579 			 25135/9 		  71.0%
	  5B08436 			 ‘ 		  0 %
	  4AF9553 			 SAF9553 		  86.0%
	  2B52739 			 7851133 		  28.999999999999996%
	  7B00844 			 7B00844 		  100 %
	  6B68384 			 BEB 		  0 %
	  7B15714 			 E75710 		  0 %
	  7B29430 			 7529430 		  86.0%
	  7B61623 			 7B61623 		  100 %
	  2B89928 			 TRINE 		  0 %
	  6B67033 			 10 		  0 %
	  9B84759 			  		  0 %
	  1BB8851 			 158° 		  0 %
	  6B50430 			  		  0 %
	  6B00320 			 6800920 		  71.0%
	  4B45969 			 1B5969 		  0 %
	  7B58307 			 7858307 		  86.0%
	  BZC4939 			 BZC4939 		  100 %
	  1BB6338 			 1BB6338 		  100 %
	  1BA0888 			 1BA0888 		  100 %
	  9B49365 			 9849365 		  86.0%
	  6B26050 			 6826050 		  86.0%
	  8B64702 			 8B64702 		  100 %
	  3Z63187 			 3263187 		  86.0%
	  4AZ3802 			 4AZ3802 		  100 %
	  7B57267 			 37B5 		  0 %
	  2Z52689 			 2752686 		  71.0%
	  6B88304 			 GBB8304 		  71.0%
	  4E02304 			 SEO2304 		  71.0%
	  7B40988 			 B780988 		  56.99999999999999%
	  7B94261 			 7594261 		  86.0%
	  5B11149 			 5811149 		  86.0%
	  2B74289 			 289 		  0 %
	  9B64064 			 9B64064 		  100 %
	  1B85815 			 1B85815 		  100 %
	  7B98453 			 G0 		  0 %
	  9B59729 			 B9723 		  0 %
	  5B11149 			 19 		  0 %
	  5B11300 			  		  0 %
	  1BA3428 			 IBA398 		  0 %
	  1BD0679 			 1BD0679 		  100 %
	  5B82908 			 5882908 		  86.0%
	  4E02304 			 107304 		  0 %
	  9B43104 			 231U4 		  0 %
	  BZL4085 			 T408 		  0 %
	  7B14095 			 7814095 		  86.0%
	  1BA2905 			 MB2905 		  0 %
	  6C71390 			 671390 		  0 %
	  8B46851 			 6B4680! 		  56.99999999999999%
	  6B45495 			 T548 		  0 %
	  4B82840 			 482640 		  0 %
	  4M34462 			  		  0 %
	  6M04291 			  		  0 %
	  5B05531 			 5505531 		  86.0%
	  5Z15057 			  		  0 %
	  4Z42407 			 2407 		  0 %
	  3S86899 			 3586899 		  86.0%
	  9B33297 			 9833297 		  86.0%
	  7B66657 			 657 		  0 %
	  7B27254 			 /B27254 		  86.0%
	  4AX9660 			 4AX9660 		  100 %
	  3B60372 			 3860372 		  86.0%
	  1B61178 			 1861178 		  86.0%
	  9B88363 			 9868363 		  71.0%
	  7B12151 			 7812151 		  86.0%
	  7B12151 			 7812151 		  86.0%
	  9B30130 			 $830130 		  71.0%
	  9B30130 			 9830130 		  86.0%
	  1BA1114 			 1BA1114 		  100 %
	  9B88363 			 9B88363 		  100 %
	  5B06256 			 5806256 		  86.0%
	  1BA1114 			 BBA1114 		  86.0%
	  9B30034 			 930034 		  0 %
	  5B20773 			 5B20773 		  100 %
	  8B26229 			 88?6123 		  43.0%
	  1B37208 			 10 		  0 %
	  2Z52689 			 2752689 		  86.0%
	  2B46175 			 2842617 		  28.999999999999996%
	  7B63766 			 BG38 		  0 %
	  B0L3403 			 T50 		  0 %
	  2B57127 			 E 		  0 %
	  1BC6703 			 6703 		  0 %
	  7B11607 			 ” 		  0 %
	  4B07003 			 4307003 		  86.0%
	  8B53308 			 5352908 		  43.0%
	  9B13973 			 913973 		  0 %
	  5B57928 			  		  0 %
	  5B99527 			 902/ 		  0 %
	  9B83305 			 I83 		  0 %
	  3SA6413 			 I56413 		  0 %
	  4AF9553 			 BAF 		  0 %
	  1Z35206 			 123500 		  0 %
	  8B41958 			 BB1958 		  0 %
	  3Z63187 			 3263187 		  86.0%
	  2SZ4642 			 5724642 		  56.99999999999999%
	  6B89358 			 6B89358 		  100 %
	  2B38280 			 7538280 		  71.0%
	  6B34859 			 6B34859 		  100 %
	  4B76148 			 B76148 		  0 %
	  8B81013 			 5681013 		  71.0%
	  7B88517 			 7B8891/ 		  71.0%
	  1B27247 			 F12724 		  0 %
	  BZP6480 			 6460 		  0 %
	  1BA8370 			 15 		  0 %
	  BZP6480 			 P6460 		  0 %
	  5J04027 			 504027 		  0 %
	  7B65585 			 7565585 		  86.0%
	  9B91420 			 IBS1420 		  71.0%
	  8B60552 			 886055 		  0 %
	  5B38434 			 15B3B434 		  0 %
	  7B44167 			 7B44167 		  100 %
	  3B41980 			 B4°1980 		  56.99999999999999%
	  8B69588 			 28B69 		  0 %
	  2AX4938 			 2AX4939 		  86.0%
	  7B30135 			 7B30135 		  100 %
	  2AX4938 			 AX 		  0 %
	  5B77379 			 55727379 		  0 %
	  7B73485 			 7873485 		  86.0%
	  BZL3400 			 BZ3400 		  0 %
	  4B60620 			 4B60620 		  100 %
	  3B09932 			 3309932 		  86.0%
	  8T77462 			 7462 		  0 %
	  9T19202 			 $ 		  0 %
	  9B45193 			 9B45193 		  100 %
	  1B70230 			 170230 		  0 %
	  8B47318 			 18 		  0 %
	  1BI2138 			 1B2138 		  0 %
	  7B46204 			 7846204 		  86.0%
	  5B23615 			 5523615 		  86.0%
	  2B76686 			 237G685 		  56.99999999999999%
	  8B03653 			 802653 		  0 %
	  1E59706 			 1S 		  0 %
	  1BB9110 			 B188 		  0 %
	  5B24546 			 5B24546° 		  0 %
	  9B29274 			 982$21 		  0 %
	  9B13772 			 L72 		  0 %
	  8B77932 			 R 		  0 %
	  2B97325 			 2897325 		  86.0%
	  7B76600 			  		  0 %
	  9B98777 			 598777 		  0 %
	  2B75836 			 WES 		  0 %
	  8B22920 			 8822920 		  86.0%
	  1B05002 			 180 		  0 %
	  7B31947 			 D 		  0 %
	  9B14015 			 9B4015 		  0 %
	  BZL5685 			 685 		  0 %
	  2E67175 			 ~ 		  0 %
	  7B35438 			 7B35438 		  100 %
	  3K29729 			 ALE 		  0 %
	  4B96914 			 89691 		  0 %
	  8B86266 			  		  0 %
	  6B03119 			 6B03119 		  100 %
	  8B86266 			 8886256 		  71.0%
	  7B34216 			 17834216 		  0 %
	  8T84065 			 TT84065 		  86.0%
	  4AC5710 			 R70} 		  0 %
	  1BC1046 			 F5 		  0 %
	  8B62184 			  		  0 %
	  3B60854 			 3S085 		  0 %
	  4B64261 			 W 		  0 %
	  4B86157 			  		  0 %
	  4B82295 			 LEG 		  0 %
	  4B22781 			 4B22781 		  100 %
	  7B72729 			 7B/2723 		  71.0%
	  BMZ3933 			 BNZ3933 		  86.0%
	  BZB1922 			 BZB1922 		  100 %
	  2M25948 			 B225948 		  71.0%
	  7B30788 			 /B30788 		  86.0%
	  7B90990 			 89°0990 		  56.99999999999999%
	  6B70990 			 6B70990 		  100 %
	  7B75285 			 7875285 		  86.0%
	  3B25229 			 3525229 		  86.0%
	  7B59066 			 7859066 		  86.0%
	  7B14095 			 B4095 		  0 %
	  7B89545 			 7589545 		  86.0%
	  8B66512 			 B12 		  0 %
	  1BB7325 			 1867325 		  71.0%
	  2B73768 			 2B73768 		  100 %
	  6B24616 			 5821616 		  56.99999999999999%
	  5B26675 			 5B26675 		  100 %
	  7B78280 			 7878280 		  86.0%
	  5H68504 			 L5H8504 		  56.99999999999999%
	  9B86906 			 9B86906 		  100 %
	  4M26776 			 4M267/6 		  86.0%
	  1B49951 			 BL995 		  0 %
	  8B91698 			 8891698 		  86.0%
	  1BC5138 			 IBC5138 		  86.0%
	  8T43418 			  		  0 %
	  LL435JG 			 1643596 		  43.0%
	  8T32290 			 8132290 		  86.0%
	  6B48927 			 6B48927 		  100 %
	  B13AEB 			 B13AEB 		  100 %
	  9B65431 			 9865431 		  86.0%
	  FMR4437 			 MFMR 		  0 %
	  3M46918 			 M46918 		  0 %
	  8B70688 			 9870688 		  71.0%
	  1BC5191 			 P5191 		  0 %
	  1BC0525 			 BBC052 		  0 %
	  3B37902 			 130 		  0 %
	  3B88729 			 386729 		  0 %
	  1BB8887 			 1BB8887 		  100 %
	  5B97425 			 5B97425 		  100 %
	  4B42029 			 4B42023 		  86.0%
	  1B87819 			 1B87819 		  100 %
	  5B97425 			 BBS7425 		  71.0%
	  5B29670 			 5B29670 		  100 %
	  7B27150 			 7827150 		  86.0%
	  4AT7962 			 GAT7962 		  86.0%
	  7B08199 			 7B08199 		  100 %
	  7B14156 			 7B14156 		  100 %
	  5M56740 			  		  0 %
	  5M56740 			 0 		  0 %
	  7B14156 			  		  0 %
	  9T33663 			 9133663 		  86.0%
	  B8547 			 B8547 		  100 %
	  7B31505 			 7631505 		  86.0%
	  DT612BC 			 DT*612BC 		  0 %
	  7B32443 			 7832443 		  86.0%
	  7B30332 			 032 		  0 %
	  1BD4657 			 18D4657 		  86.0%
	  6B28800 			 6B28800 		  100 %
	  3T16754 			 3116754 		  86.0%
	  BMZ0815 			 BYZ0815 		  86.0%
	  3B95265 			 3B95265 		  100 %
	  6B23451 			 55235 		  0 %
	  9B54817 			  		  0 %
	  8B10162 			 02 		  0 %
	  3B48296 			 7 		  0 %
	  2Z96418 			 8 		  0 %
	  2E73609 			  		  0 %
	  8B10732 			 SBTO732 		  56.99999999999999%
	  BZE7277 			 BJ 		  0 %
	  9B43101 			 95/3101 		  71.0%
	  4B01832 			 URNOSL 		  0 %
	  1B07846 			  		  0 %
	  4B56396 			 O856396 		  71.0%
	  9B78701 			 9B78701 		  100 %
	  BZJ2909 			 BL2309 		  0 %
	  9B46988 			 9B63H 		  0 %
	  5H85564 			 5185564 		  86.0%
	  3AJ8930 			 800 		  0 %
	  BZ04966 			 1955 		  0 %
	  B8121 			  		  0 %
	  4B83172 			 583172 		  0 %
	  1BI1710 			 7 		  0 %
	  3B60832 			 766832 		  0 %
	  2B45270 			 28462/0 		  56.99999999999999%
	  6B84949 			 P4949 		  0 %
	  6B84949 			 6B8436% 		  56.99999999999999%
	  3B75763 			 3B75763 		  100 %
	  8T52099 			 2099 		  0 %
	  2AB5896 			 ANB5890 		  56.99999999999999%
	  4B46694 			 4B46694 		  100 %
	  4B84489 			 LB84489 		  86.0%
	  BZN1065 			 F108 		  0 %
	  2AN1991 			  		  0 %
	  SI625BC 			 F7025 		  0 %
	  9B36205 			 9536205 		  86.0%
	  9B13198 			  		  0 %
	  8B68508 			 B8508 		  0 %
	  8B68508 			 T8608 		  0 %
	  9B14541 			  		  0 %
	  BZ05042 			 B205042 		  86.0%
	  BZI0346 			 BLI0346 		  86.0%
	  3B30411 			 3830411 		  86.0%
	  6B48288 			 6648288 		  86.0%
	  9B85289 			 9B85289 		  100 %
	  2B90178 			 2890178 		  86.0%
	  7B59839 			 7559639 		  71.0%
	  7B11123 			 7811123 		  86.0%

Counter is a sub-class which is used to count hashable objects.by using counter we can count for how many plates it calculated 100% accuracy
output is here:

Counter({'86.0%': 69,
         '100 %': 58,
         '0 %': 151,
         '71.0%': 28,
         '56.99999999999999%': 14,
         '14.000000000000002%': 1,
         '28.999999999999996%': 2,
         '43.0%': 3})
         
its predicted 100% accuracy for 58 plates and 86% accuracy for 69 plats and so on...         








