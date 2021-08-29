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
Un șir de caractere (string) este un caz particular de buffer. 