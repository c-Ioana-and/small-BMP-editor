# BMP Editor

Un mic proiect care poate citi și modifica fișiere BMP, în funcție de input-ul dat de utilizator.

# Scop
Întelegerea formatului intern al unui fișier BMP, citirea unui fișier în cod. 

# Implementare

## Operațiile suportate:
Dacă primul cuvânt din input este o operație (edit, save...), atunci sar peste acel cuvant, ajungand la calea fisierului care trebuie
editat/salvat. Pointez către calea fisierului folosind pointerul filename.

### Edit
Deschid fisierul și încarc informatiile imaginii bmp in memorie: partea de file header, info header și matricea de pixeli (folosind funcția read_image). 
Dacă pe linie avem un număr nedivizibil cu 4 de biți (biții fiind rezultatul produsului width * sizeof(RGB) - un bit 
pentru fiecare culoare), atunci trebuie să am grijă să nu citesc biții de padding. Pentru fiecare linie, după „width” 
pixeli, setez pointerul din fișier după biții de padding folosing fseek. Nu eliberez memoria deoarece aștept ca matricea 
să fie modificată după alte operații.
### Save
trebuie să construiesc un nou fișier la calea dată și să scriu tot ce se află în fișierul din edit (fwrite), 
având grijă să pun la loc biții de padding în cazul în care lungimea pozei nu este divizibilă cu 4 (am scris 0 pe fiecare poziție de padding).
Odată ce am creat fișierul cerut, închid fișierul din edit (in) și cel din save (out), dau free pointerului care stochează pixelii 
din imaginea citită (folosind funcția free_memory) și pointerului filename, deoarece nu mai am niciun fișier de citit 
sau creat. Odata ce nu mai am comenzi de citit (am ajuns la „quit”), atunci eliberez pointerul care stoca linia citită 
de la tastatură.
### Insert
Refolosesc pointerul filename pentru a reține calea fișierului din care vom extrage poza pentru a o insera în fișierul 
din edit. Citesc headerele care conține detalii despre fișier și poza în sine, citesc matricea de pixeli și cele două 
coordonate din care începe inserarea.
Folosind funcția insert, inserez în matricea pixelilor din fișierul de edit elementele din matricea pixelilor pentru 
fișierul de insert, ținând cont că la început am citit aceste matrici de jos în sus și că trebuie
să am grijă să nu ies cumva din matricea de edit. 
Returnez matricea din edit modificată, eliberez memoria folosită pentru fișierul de insert și închid fișierul.
### Set draw_color 
- citesc R G B și le stochez într-o structură (RGB) pentru a le folosi mai târziu
### Set line_width
- citesc grosimea liniei (variabila l_width)
### Draw line
- citesc coordonatele celor două puncte și rețin grosimea liniei într-o structură (line) și apelez 
funcția colour_lines. Această funcție calculează care este intervalul maxim dintre |x1- x0| și |y1 - y0| 
și apelează fie funcția which_collum (dacă trebuie să parcurg dreapta de la x0 la x1), fie la which_line 
(dacă parcurg dreapta de la y0 la y1). 
Această funcție calculează pentru fiecare punct ce valoare are o coordonată în funcție de cealaltă folosind ecuația dreptei. 
Am luat în calcul cazul în care y - y0/ x - x0 dă negativ deoarece partea întreagă dintr-un nr. negativ nu este afișată corect
direct prin folosirea variabilelor de tip întreg, trebuie să scad cu -1.
După ce calculez o coordonată, apelez funcția fill_rec care asigură grosimea liniei, considerând fiecare 
coordonată (x, y) centrul unui pătrat. Pentru a colora un pixel, folosesc fcț. fill_RGB, având grijă ca
cele două coordonate date ca parametru să nu iasă din matricea de pixeli.
## Draw rectangle
- folosesc aceleași funcții pentru a trage linii din cele patru puncte ale pătratului 
pe care le calculez folosind lungimea și lățimea citită de la tastatură
## Draw triangle
- folosesc o altă structură de tip line (l1) și trag trei linii folosindu-mă de cele trei prechi de 
coordonate citite, alternând între ele folosind funcția assing_coords.
## Fill
Folosesc două structuri de tip RGB pentru a stoca culoarea pixelului cu coordonatele citite de la
tastatură și culoarea finală, creez un pointer la structura de tip image (care conține înălțimea,
lungimea și matricea pixelilor) și folosesc funcția fill.
Această funcție este recursivă și verifică trei aspecte:
- coordonatele date să fie valide
- să nu marcheze un pixel care nu are culoarea pixelului inițial
- să nu fie un pixel deja colorat cu culoarea cerută
Dacă totul este ok, stochez culoarea și mă mut la pixelii de jos, sus, dreapta și stânga.
