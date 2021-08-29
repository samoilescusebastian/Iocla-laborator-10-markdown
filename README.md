# Laborator 10: Gestiunea bufferelor. Buffer overflow

Acest laborator își propune prezentarea conceptului de buffere în C și limbaj de asamblare împreună cu operațiile specifice acestora,
dar și vulnerabilitățile pe care acestea le au și cum pot ele să fie exploatate de un potențial atacator prin folosirea unui program
cu scopul de a ataca un sistem sau de a obține informații la care în mod normal nu ar avea acces.

Obiective:
  - Prezentarea conceptelor de buffer și buffer overflow
  - Exemple de atacuri de tip buffer overflow
  - Prezentarea unor modalități de securizare a programelor pentru evitarea atacurilor de tip buffer overflow

## Buffer. Buffer overflow

### Ce este un buffer?

Un buffer este o zonă de memorie definită printr-o adresă de start și o dimensiune. Fie N dimensiunea bufferului,
adică numărul de elemente. Dimensiunea totală a bufferului este N x dimensiunea unui element.
Un șir de caractere (*string*) este un caz particular de buffer.

### Ce este un buffer overflow?

Un buffer overflow este un eveniment care se produce atunci când în parcurgerea unui buffer se depășește limita superioară,
adică poziția ultimului element (v[N - 1]). Un buffer overflow este un caz particular de *index out of bounds*,
în care vectorul poate fi accesat folosind și indecși negativi. Multe funcții din C nu verifică dimensiunea bufferelor cu care lucrează,
acestea cauzând erori de tip buffer overflow atunci când sunt apelate. Câteva exemple de astfel de funcții sunt:
  - [memcpy](http://www.cplusplus.com/reference/cstring/memcpy/)
  - [strcpy](https://www.cplusplus.com/reference/cstring/strcpy/)
  - [fgets](http://www.cplusplus.com/reference/cstdio/fgets/)

Un exemplu clasic de buffer overflow este dat de următorul cod:
```C
char buffer[32];
fgets(buffer, 64, stdin);
```
 În urma execuției acestui cod vom obține un buffer overflow care ar putea conduce chiar la o eroare de tip *Segmentation Fault*, însă acest lucru nu este garantat. Totul depinde de poziția bufferului pe stivă și de ceea ce se va suprascrie prin cei 32 de octeți ce depășesc dimensiunea bufferului. Mai multe detalii despre ceea ce se va suprascrie, dar și modalitatea prin care se va face acest lucru veți descoperi în timpul rezolvării exercițiilor.

## Atacuri de tip buffer overflow

  ### Cum este folosit buffer overflow?