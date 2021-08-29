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

## Pregătire infrastructură

> **IMPORTANT:** 
> În cadrul laboratoarelor vom folosi repository-ul de git al materiei IOCLA - https://github.com/systems-cs-pub-ro/iocla.
> Repository-ul este clonat pe desktop-ul mașinii virtuale. Pentru a îl actualiza, folosiți comanda git pull origin master din
> interiorul directorului în care se află repository-ul (~/Desktop/iocla). Recomandarea este să îl actualizați cât mai frecvent,
> înainte să începeți lucrul, pentru a vă asigura că aveți versiunea cea mai recentă.

> Dacă doriți să descărcați repository-ul în altă locație, folosiți comanda git clone https://github.com/systems-cs-pub-ro/iocla ${target}. 

> Pentru mai multe informații despre folosirea utilitarului git, urmați ghidul de la [Git Immersion](https://gitimmersion.com/).

Pentru desfășurarea acestui laborator vom folosi interfața în linia de comandă.

Pe parcursul laboratorului, în linia de comandă, vom folosi: 
  - asamblorul ```nasm```
  - comanda ```gcc``` pe post de linker
  - ```objdump``` și ```ghidra``` pentru dezasamblarea fișierelor obiect și executabile
  - ```gdb``` pentru analiza dinamică, investigație și debugging

În general nu va fi nevoie să dați comenzi de compilare. Fiecare director cuprinde un Makefile pe care îl puteți rula
pentru a compila în mod automat fișierele cod sursă limbaj de asamblare sau C.

Înainte de a începe laboratorul, alocați-vă 1-2 minute să reparcurgeți diagrama de mai jos ce arată structura stivei în cazul unui apel de funcție.

![Image](https://ocw.cs.pub.ro/courses/_media/iocla/laboratoare/stack-in-function-call.png?cache=)


## 1. Tutorial: Folosirea unui buffer în zona de date

Accesați, în linia de comandă, directorul ```1-data-buffer/``` din arhiva de resurse a laboratorului și consultați fișierul ```data_buffer.asm```.
În acest fișier se găsește un program care populează un buffer cu informații și apoi le afișează.

Consultați cu atenție programul, apoi compilați-l folosind comanda:

```make```

Observați că în urma comenzii de compilare de mai sus au rezultat un fișier obiect și un fișier executabil, prin rularea comenzii:

```ls```

Rulați programul prin intermediul fișierului executabil, adică folosind comanda:

```./data_buffer```

Observați comportamentul programului în funcție de codul său.


## 2. Tutorial: Folosirea unui buffer pe stivă

Accesați directorul ```2-3-4-stack-buffer/``` din arhiva de resurse a laboratorului și consultați fișierul ```stack_buffer.asm```.
În acest fișier se găsește un program care populează un buffer cu informații și apoi le afișează.
Este similar celui de mai sus doar că acum buffer-ul este alocat pe stivă.

Consultați cu atenție programul, apoi compilați-l folosind comanda:

```make```

apoi rulați-l folosind comanda:

```./stack_buffer```

Observați comportamentul programului în funcție de codul său.

Pe lângă buffer am mai alocat o variabilă locală pe 4 octeți, accesibilă la adresa ```ebp-4```.
Este inițializată la valoarea ```0xCAFEBABE```. Această variabilă va fi importantă mai târziu.
Ce este relevant acum este să știm că această variabilă este în memorie **imediat după buffer**: când se trece de limita buffer-ului se ajunge la această variabilă.

Care este diferenta intre cele 2 programe inspectate pana acum?


## 3. Citirea de date dincolo de dimensiunea buffer-ului

Acum că am văzut cum arată buffer-ul în memorie și unde este plasată variabila,
actualizați programul ```stack_buffer.asm``` pentru ca secvența de afișare a buffer-ului
(cea din jurul etichetei ```print_byte```) să ducă și la afișarea octeților variabilei.
Adică trebuie să citiți date dincolo de dimensiunea buffer-ului (și să le afișați).
Este un caz de buffer overflow de citire, cu obiectiv de **information leak**: aflarea de informații din memorie.

> **TIP**
> Nu e ceva complicat, trebuie doar să "instruiți" secvența de afișare să folosească altă limită pentru afișare,
> nu limita curentă de 64 de octeți.

Afișați și alte informații dincolo chiar de variabila locală.
Ce informație vine pe stivă după variabila locală (următorii 4 octeți)? Dar următorii 4 octeți după?


## 4. Scrierea de date dincolo de dimensiunea buffer-ului

Pe baza experienței de mai sus, realizați modificări pentru ca valoarea variabilei să fie ```0xDEADBEEF```
(în loc de ```0xCAFEBABE``` cum este la început) fără a modifica însă explicit valoarea variabilei.
Folosiți-vă de modificarea buffer-ului și de registrul ```ebx``` în care am stocat adresa de început a buffer-ului.

> **TIP**
>  Din nou, nu este ceva complicat. Trebuie să vă folosiți de valoarea ```ebx``` și un offset ca să scrieți valoarea ```0xDEADBEEF``` la acea adresă.
> Adică folosiți o construcție de forma:
> ```Assembly
> mov byte [ebx+TODO], TODO
> ```
> Realizați acest lucru după secvența de inițializare a buffer-ului (după instrucțiunea ```jl fill_byte```).

La o rezolvare corectă a acestui exercițiu, programul va afișa valoarea ```0xDEADBEEF``` pentru variabila locală.


## 5. Tutorial: Citirea de date de la intrarea standard

Accesați directorul ```5-6-read-stdin/``` din arhiva de resurse a laboratorului și consultați fișierul ```read_stdin.asm```.
În acest fișier se găsește un program care folosește apelul ```gets``` ca să citească informații de la intrarea standard
într-un buffer de pe stivă. La fel ca în cazul precedent am alocat o variabilă locală pe 4 octeți imediat după buffer-ul de pe stivă.

Consultați cu atenție programul, apoi compilați-l folosind comanda:

```make```

apoi rulați-l folosind comanda:

```./read_stdin```

Observați comportamentul programului funcție de input-ul primit.

## 6. Buffer overflow cu date de la intrarea standard

Funcția [gets](https://man7.org/linux/man-pages/man3/gets.3.html) este o funcție care este practic interzisă în programele C
din cauza vulnerabilității mari a acesteia: nu verifică limitele buffer-ului în care se face citirea,
putând fi ușor folosită pentru buffer overflow.

Pentru aceasta transmiteți șirul de intrare corespunzător pentru ca valoarea afișată pentru variabila locală să nu mai fie
```0xCAFEBABE```, ci să fie ```0x574F4C46``` (valorile ASCII în hexazecimal pentru ```FLOW```). 

> **IMPORTANT**
> Nu modificați codul în limbaj de asamblare.
> Transmiteți șirul de intrare în format corespunzător la intrarea standard pentru a genera un buffer overflow și pentru a obține rezultatul cerut. 

> **WARNING**
> Nu scrieți șirul ```"574F4C46"```. Acesta e un șir care ocupă ```8``` octeți.
> Trebuie să scrieți reprezentarea ASCII a numărului ```0x574F4C46``` adică ```FLOW```: 
> ```0x57``` este ```W```, ```0x4F``` este ```O```, ```0x4C``` este ```L``` iar ```0x46 ``` este ```F```.

> **TIP**
> x86 este o arhitectură little endian. Adică șirul ```"FLOW"```, având corespondența caracter-cod ASCII
> ```F```: ```0x46```, ```L```: ```0x4C```, ```O```: ```0x4F```, ```W```: ```0x57``` va fi stocat în memorie pe 4 octeți ca 0x574F4C46.