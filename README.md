# price-comparing-application
import requests
from bs4 import BeautifulSoup
headers = {'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'}
from tkinter import *
from PIL import ImageTk, Image
from googlesearch import search 
import requests
from tkinter import messagebox

from google_images_download import google_images_download 
def flipkart(b):
    global headers

    url=b   
    flipkartpage=requests.get(url,headers=headers).text
    soup=BeautifulSoup(flipkartpage,'lxml')
    try:
        price=soup.find('div',class_='_1vC4OE _3qQ9m1').text
    except:
        price=soup.find(class_="_1vC4OE _2rQ-NK").text
    price=price[1:].split(',')
    price="".join(price)
    price =float(price)
    title=soup.find(class_='_35KyD6').text
    # print(price)
    return title, price
def amazon(a):
    global headers

    url=a
    amazonpage=requests.get(url,headers=headers)
    soup=BeautifulSoup(amazonpage.content,'html.parser')
    try:
        price=soup.find(id='priceblock_ourprice').text.strip()
        price=price[2:].split(",")
        price="".join(price)
    except:
        price=soup.find(class_='a-color-price').text.strip()
        p=[x for  x in price if x !=',' ]
        price="".join(p)
        print(price)
    
    try:

        price=float(price)
    except:
        messagebox.showinfo('amazon','not available')
    title=soup.find(id='productTitle').text.strip()
    
    title=" ".join(title[:2])

    print(price)
    return  price

def download_():
     
   global path
   response = google_images_download.googleimagesdownload()
   keywords = searchvar.get()+" image"
   arguments = {"keywords":keywords,"limit":1}
   paths = response.download(arguments)
   print(paths)  
   path=paths[0][keywords][0]

def urlsetting():
    item=searchvar.get()
    a_query='amazon '+item
    b_query='flipkart '+item
    
  
    a=list( search(a_query, tld="co.in", num=10, stop=10, pause=0.2))[2]
    print(a)
    b=list(search (b_query,tld='com', num=10, stop=5 , pause=3))[1]
    print(b)
    
    global path

    b1=amazon(a)
    
    a2,b2=flipkart(b)
    download_()
    global img, root
    
    img_=Image.open(path)
    
    img=img_.resize((90,120),Image.ANTIALIAS)
    img = ImageTk.PhotoImage(img)    
    panel = Label(fr2, image=img)
    panel.grid(row=0,column=0)
    imaagelbel=Label(fr2,text=a2)
    imaagelbel.grid(row=1,column=0)
    price=Label(fr2,text=str(b1))
    price.grid(row=2,column=0)
    panel=Label(fr2,image=img)
    imaagelbel=Label(fr2,text=a2)
    panel.grid(row=0,column=1)
    imaagelbel.grid(row=1,column=1)
    price=Label(fr2,text=str(b2))
    price.grid(row=2,column=1)
    


    
root = Tk()
searchvar=StringVar()
searchvar.set('item name')
fr1=LabelFrame(root,text='comparison')
fr1.pack(padx=10,pady=10,ipadx=10,ipady=10)
searchlabel=Entry(fr1,textvariable=searchvar)
searchlabel.grid(row=0,column=0)
bt1=Button(fr1,text='comapare',command=urlsetting)
bt1.grid(row=0,column=1)

fr2=Frame(fr1)

fr2.grid(row=1,column=0,columnspan=2)


root.mainloop()
