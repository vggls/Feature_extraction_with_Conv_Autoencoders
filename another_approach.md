For min_signal = 20000 samples the spectograms are of shape (49,200).

The autoencoder was again compiled with Binary Crossentropy and Adam at 0.001 learning rate and its loss history was as follows :

<img src="https://user-images.githubusercontent.com/55101427/175825772-9bcb5b22-b384-44eb-8670-ebbd0c8dece8.png" height="220" width="310" />

and the encoder summary was :

<img src="https://user-images.githubusercontent.com/55101427/175825819-019c2ae2-79e2-49e3-bb53-5ed1681f4fd1.png" height="330" width="330" />


Finally, the results obtained are as follows :

<p float="left">
     <img src="https://user-images.githubusercontent.com/55101427/175825101-1b977604-85d2-43d3-9576-a6197116b2f8.png" height="220" width="310" />
     <img src="https://user-images.githubusercontent.com/55101427/175825122-002988f0-d574-4e68-8175-004bbab86e24.png" height="220" width="310" />
     <img src="https://user-images.githubusercontent.com/55101427/175825151-8ca6c022-d98b-44e9-ac6b-399ea4030323.png" height="220" width="370" />
   </p>
   
<p float="left">
     <img src="https://user-images.githubusercontent.com/55101427/175825168-92cd6fd6-293c-4106-88c7-a6fdf5dc9314.png" height="220" width="330" />
     <img src="https://user-images.githubusercontent.com/55101427/175825190-e779fe13-c7dc-48f3-9bd7-6ae8d6de4c8c.png" height="220" width="330" />
     <img src="https://user-images.githubusercontent.com/55101427/175825205-9d1b8f84-2616-4764-ad9d-833213c7ae43.png" height="220" width="330" />
   </p>


We note that in all classifier cases the PyAudioAnalysis features outperform the Autoencoder ones in most cases. There is not a "large" region as in the Random Forest
min_signal = 30K case, where the Autoencoder features prevail.
