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
 În urma execuției acestui cod vom obține un buffer overflow care ar putea conduce chiar la o eroare de tip *Segmentation Fault*,
 însă acest lucru nu este garantat. Totul depinde de poziția bufferului pe stivă și de ceea ce se va suprascrie prin cei 32 de octeți
 ce depășesc dimensiunea bufferului. Mai multe detalii despre ceea ce se va suprascrie,
 dar și modalitatea prin care se va face acest lucru veți descoperi în timpul rezolvării exercițiilor.

## Atacuri de tip buffer overflow

### Cum este folosit buffer overflow?

Buffer overflow poate fi exploatat de un potențial atacator pentru a suprascrie anumite date din cadrul unui program,
afectând fluxul de execuție și oferind anumite beneficii atacatorului.
Cel mai adesea, un atacator inițiază un atac de tip buffer overflow cu scopul de a obține acces la date confidențiale,
la care, în mod normal, un utilizator obișnuit nu ar avea acces.

Atacurile de tip buffer overflow sunt folosite în general pe buffere statice, stocate la nivel de stivă.
Acest lucru se datorează faptului că pe stivă, pe lângă datele programului, se stochează și adrese de retur în urma apelurilor de funcții (vezi laboratorul 7).
Aceste adrese pot fi suprascrise printr-un atac de tip buffer overflow, caz în care poate fi alterat fluxul de execuție a programului.
Prin suprascrierea adresei de retur, odată cu încheierea execuției funcției curente nu se va mai reveni la execuția funcției apelante,
ci se va „sări” la o altă adresă din cadrul executabilului de unde se va continua execuția.
Acest eveniment poate conduce la comportament nedefinit al programului (*undefined behaviour*) dacă adresa la care se „sare” nu a fost calculată corect.

Scopul unui atacator este acela de a prelua controlul unui sistem prin obținerea accesului la un shell din care să poată rula comenzi.
Acest lucru se poate realiza prin suprascrierea adresei de retur, folosind un apel de sistem prin intermediul căruia se poate deschide
un shell pe sistemul pe care executabilul rulează (mai multe detalii la cursul de SO).

### Cum ne protejăm de atacuri de tip buffer overflow?

Există multe modalități de a proteja un executabil de acest tip de atacuri. Pe majoritatea le veți studia în amănunt la cursul de SO anul viitor.
O bună practică împotriva acestui tip de atac este de a evita folosirea unor funcții nesigure, precum cele prezentate mai sus.
Mai multe detalii despre bune practici împotriva atacurilor de tip buffer overflow puteți găsi [aici](https://security.web.cern.ch/recommendations/en/codetools/c.shtml).

De multe ori, bunele practici se dovedesc a fi insuficiente în „lupta” împotriva atacatorilor,
motiv pentru care au fost inventate mai multe mecanisme de protecție a executabilelor prin manipularea codului
și a poziției acestuia în cadrul executabilului (*Position Independent Code* - [PIC](https://en.wikipedia.org/wiki/Position-independent_code)),
prin randomizarea adreselor (*Address Space Layout Randomization* - [ASLR](https://en.wikipedia.org/wiki/Address_space_layout_randomization))
sau prin introducerea unor verificări suplimentare în cod pentru a detecta eventuale atacuri.
Aceste verificări se realizează prin introducerea unor valori speciale, numite **canary** pe stivă,
între buffer și adresa de retur a funcției. Aceste valori sunt generate și plasate în cadrul executabilului de către compilator
și diferă la fiecare rularea executabilului. În momentul în care un atacator vrea să suprascrie adresa de retur se va suprascrie
și valoarea canary și înainte de a se părăsi apelul funcției curente se va verifica dacă acea valoare a fost modificată sau nu.
Dacă a fost modificată înseamnă că a avut loc un buffer overflow și execuția programului va fi întreruptă.
Acest mecanism se numește **Stack Smashing Protection** sau **Stack Guard**. Mai multe detalii despre Stack Guard,
dar și despre atacuri de tip buffer overflow puteți găsi [aici](https://en.wikipedia.org/wiki/Buffer_overflow). 