# Limno_PUCE_ddRAD

## ddRAD LimnoPUCE

### 1. Demultiplexing

Iluimina reconoce el primer y el sitio de corte (restriction site). Si no reconoce al primer, se borra la lectura para mejorar la calidad de los datos.

_Phred_..(no alcance a anotar bien..)

Ilumina generó ocho pools que fueron entregados en archivos comprimidos formato **.fq.gz** , cada uno de estos archivos (pools) contaba de dos lecturas (R1 y R2). Dentro de cada lectura las secuencias obtenidas estaban asociadas al adaptador específico de cada taxón (individuo). Mientras que las secuencias de cada individuo se encuentra en cada pool. Los archivos **.rem** (removed) son archivos que no han calificado para el Ilumina. Aunque no son  muy útiles son guardados junto con los demas archivos archivos. N

La calidad de cada lectura fue revisada con la ayuda del software FastQ, el cual descomprime los archivos **.gzip** y analiza algunos parámetros de calidad del secuenciamiento... en construcción..!

La finalidad del demultiplexing es la de entregar una identidad a cada secuencia de cada pool. Es decir, identificar las lecturas de cada individuo mediante la asociación de los adaptadores de cada pool con la información codificada de cada muestra secuenciada (códigos durante la extracción de ADN). En nuestro caso, trabajaremos solamente con la primera lectura (lectura simple) y también con ambas en conjunto (lectura doble). Todo este proceso fue realizado con la ayuda del software Stacks, mediante el uso de la función process_radtag. Para lo cual preparamos una matriz con los códigos de extracción con sus respectivos barcodes (matriz "barcodes"). Esta matriz en formato de texto separado por comas fue utilizado junto con la lectura 1 del Pool1.

Usando (Mac Vero y Pool2):

**macs-MacBook-Pro-2: MingaGenomica macuser$ process_radtags -p ./raw/ -o ./process_Pool2_Read1/ -b ./barcodes_Pool2_Read1/Pool2_Read1.txt -r -c -q --inline_null --renz_1 ecoRI --renz_2 mspI**

Donde:

-_p_ Es la ubicación (carpeta) donde están las lecturas crudas para ser "procesadas". Si se usa -P se procesan ambas lecturas juntas.

-_o_ Es la ubicación donde se depositará el archivo generado al final del proceso (output).

-_b_ Archivos que serán asociados en el proceso. Es decir las lecturas de Ilumina (barcodes_Pool2_Read1) y el archivo de texto con la matriz "barcodes".

-_r_ -_c_ -_q_ Son defaults.

_--inline_null--_ Es recomendado cuando se trabaja con dos enzimas de restricción. (Aunque parece que también sale con --inline_index-- cf.).

_--Renz_1_ Enzima de restricción 1 en formato reconosible para Stacks process_radtags. En nuestro caso ecoR1.

_--Renz_2_ Enzima de restricción 2 en formato reconosible para Stacks process_radtags. En nuestro caso mspI.

Durante este proceso es importante revisar muy bien la sintaxis, pues el mínimo error caligráfico evitará que se realice el proceso. Para lo cual usamos el software bbEdit. Que no muestra caracteres escondidos.

Una vez reemplazados los barcodes de los archivos .gzip por los códigos de cada muestra, se crean carpetas para cada genero donde se depositan los respectivos archivos .gzip.

Comando _mv_ nos permite generar una copia de los archivos de nombre distinto.

_Pipe_ (linea vertical) nos permite continuar la sintyaxs de un inpùt como el out put de la suguienmte función. 

El símbolo > nos permite generar un archivo nuevo  a partir del output  de una funcion.

#### Demultiplexing Vero

####**Example code for pool 1:** 

>process\_radtags -P -b ./Barcode\_P1\_R1.txt -c -q -r --inline\_index --renz\_1 ecoRI --renz\_2 mspI -p ./raw/Pool1\_R1R2 -o ./Output\_P1\_R1R2\_B --inline\_null

>-P: Demultiplexing the two runs (R1 and R2)  
-b: Barcode file  
-p: Raw files  
-o: Output directory

>Click [here](http://catchenlab.life.illinois.edu/stacks/comp/process_radtags.php) for additional process_radtags options

####**Results - demultiplexing  with process\_radtags**  
**Pool 1:**  
27132412 total sequences  
   79572 barcode not found drops (0.3%)  
   33396 low quality read drops (0.1%)  
  190903 RAD cutsite not found drops (0.7%)  
26828541 retained reads (98.9%)

**Pool 2:**  
Total Sequences	29801616    
Barcode Not Found	95236.    
Low Quality	36857.    
RAD Cutsite Not Found	216842.         
Retained Reads	29452681

**Pool 3:**  
20637354 total sequences  
   84620 barcode not found drops (0.4%)  
   25263 low quality read drops (0.1%)  
  157634 RAD cutsite not found drops (0.8%)  
20369837 retained reads (98.7%)

**Pool 4:**  
26747308 total sequences  
   90238 barcode not found drops (0.3%)  
   33530 low quality read drops (0.1%)  
  183589 RAD cutsite not found drops (0.7%)  
26439951 retained reads (98.9%)    

**Pool 5:**  
Total Sequences	29332378.     
Barcode Not Found	103512.         
Low Quality	34778.            
RAD Cutsite Not Found	177205.      
Retained Reads	29016883

**Pool 6:**  
24683186 total sequences  
   87448 barcode not found drops (0.4%)  
   28906 low quality read drops (0.1%)  
  144436 RAD cutsite not found drops (0.6%)  
24422396 retained reads (98.9%)  

**Pool 7:**  
Total Sequences	29365106.    
Barcode Not Found	111598.       
Low Quality	36271.        
RAD Cutsite Not Found	169717.    
Retained Reads	29047520.

**Pool 8:**  
Total Sequences	24115006.     
Barcode Not Found	91558.    
Low Quality	29237.    
RAD Cutsite Not Found	167554.    
Retained Reads	23826657

**IMPORTANT NOTES:**  
If error: "(filenames can consist of letters, numbers, '.', '-' and '_')", there are probably errors in the barcode file (i.e. extra spaces or unwanted simbols).  
> * Solution: open the barcodes file with TextWrangler, click on View -> Text Display -> Show invisibles
>   * This shows spaces, line breaks, etc.  
>   * Remove excess spaces or unwanted symbols 



## 2. _denovo_ mapping

Depues del **demultiplexing >** se agrupa el contenido de los pools en una carpeta por cada taxa, antes de comezar el **_de novo_** mapping.

Durante este proceso debemos manejar los nombres de los archivos de cacad carpeta de forma agrupada para editar el texto de los nombres mediante la función _mv_ (move).

**denovo_map.pl**

Mediante el uso de  las funciones:

-_ustack_ (Pipe del denovo map)

-_cstacks_ (Pipe del ustacks)

-_gstacks_ (Pipe del ustacks)

Luego de estos pasos podemos pasar a **Populations**

Los elementos requeridos para la función denovo_map.pl son:

1- Linea de comando:

**denovo_map.pl -M 3 -n 2 -o ./Atop_Test_R1 / --popmap ./popmapfile.txt --samples ./carpeta**

2- Pop map file: Lista de los nombres de los archivos dentro de la carpeta por cada taxa, en formato .txt. Generamos el archivo output mediante: 

**ls > nombre.txt (nombre del bicho) -- samples ./samples** 

En  nuestro caso debemos generar una carpeta con solo el read 1 y otra con ambos reads (1y2 usando la función_--paired_).  Para doble lectura se debe tener en cuenta que en el archivo **pop map**, deben estar las lecturas sin el número de lectura al que corresponde, ni el punto entre el número y el nombre. Es decir, tendremos cada individuo repetido dos veces en el **pop map**, a su vez Stacks asume que los archivos impares son los reads 1 y pares los reads 2.  

El archivo .txt generado (**nombre.txt**) se abre y modifica en Excel, añadiendo una columna nueva con donde ubicamos la localidad de cada muestra (**sin encabezados**).

Luego se reemplazan los terminales (.fq.gz) de cada nombre de archivo mediante la función _buscar_reemplazar_ del Excel.

Para que el Stacks no identifique el punto terminal del formato

e.g. archivo > **001And_G14V.1.fq.gz**

se puede marcar cada sección del nombre de archivo mediante un _Grep_ usando la siguiente expresión regular en TextWrangler:

**IMG01**

#### denovo_map **Vero**
denovo_map.pl: "Wrapper" que llama a un montón de programas (e.g. ustacks, cstacks, gstacks).  
Se necesita la linea de comando completo con todos los nombres de los archivos de los outputs del demultiplexing. Se necesita además un popmap file.
>Popmap file: describe las poblaciones a las que pertenecen los individuos.

## Notas _de novo_ sequencing macroinvertebrates

### Primero: hay que crear un archivo con los codigos de los individuos y los sitios a los que pertenecen.

Para hacer esto:  

1. Hacer una lista de todos los archivos de la carpeta donde estan las muestras. Para esto:

	ls > newfilename.txt  
	**¡Importante 1!** antes se debe cambiar el directorio (con cd) para estar en la carpeta donde están los archivos (samples).  
	**¡Importante 2!** borrar los archivos innecesarios como los rem (removidos durante el demultiplexing). También, si solo se va a analizar los R1, dejar solo los archivos de read 1. 
	  
2. Editar el archivo en TextWrangler o BBEdit para borrar partes innecesarias:   

	En el nombre: **001And_G14V.1.fq.gz** no es necesario todo lo que va después del punto. Además, falta otra columna con el nombre (o código de los sitios). Para modificar todo esto:
 
![Codigo grep](https://github.com/riokoto80/Limno_PUCE_ddRAD/blob/master/Fotos/GrepTW.jpg)

De esta forma, se genera un archivo de texto con el formato correcto:

![PopMap file](https://github.com/riokoto80/Limno_PUCE_ddRAD/blob/master/Fotos/ScreenShot_PopMap.jpg)

NOTA: ¡¡¡Cuidar que los mismos sitios tengan el mismo nombre!!!
> Algunos bichos dicen "Purg" y otros "Pur"
> Se puede corregir esto con un grep:
> ... insert image

### Segundo: denovo sequencing

Se usa el programa **`denovo_map.pl`**

Código: `denovo_map.pl -M 3 -n 2 -o ./DeNovo_And_R1/ --popmap ./PopMap_And_R1.txt --samples ./Andesiops_R1`

**-o:** output directory  
**--popmap:** archivo popmap  
**--samples:** directorio donde están las muestras  
**-m, -M y -n:** son parámetros de STACKS 

Donde:

> -m: [ustacks] Minimum stack depth / minimum depth of coverage (default: 3); para generar stacks idénticos.

> -M: [ustacks] Distance allowed between stacks (default: 2); permite comparar entre los stacks creados en el parámetro -m; cuantos lugares polimórficos se permiten dentro de los individuos para formar un stack, define los heterocigotos dentro de los individuos.

> -N: [ustacks] Permite reincorporar reads (secondary) medios perdidos, para evitar fallas o malas lecturas 

> **Todo estos parámetros anteriores fueron dentro de los individuos (within).** 

> -n: Distance allowed between catalog loci (default: 1), es una comparación entre los stacks entre los individuos, define cuales son parálogos y cuales no. -n bajo para inferencias de una sola especie , para que sean considerados del mismo locus.

>**Este último es para comparaciones entre los individuos (among).** 

Para más información ver: http://catchenlab.life.illinois.edu/stacks/param_tut.php
	

### Tercero: denovo genotyping


Vamos a probar los parámetro óptimos a ser utilizados (para referencia ver página: https://github.com/pesalerno/MingaGenomica2019, donde están los pasos a seguir en nuestro proyecto 'bichitos'):

**Para Andesiops:**

> m: 4, 5 & 6
 
> M: 6 & 7

> n: 6 y 7 ( n = M & m = M + 1)

**`Que da igual a 12 combinaciones.`**

**Para Hyalella:**

> m: 5, 6 y 7

> M: 6 & 7

> n: 6 y 7 ( n = M & m = M + 1)
 	 
**`Que da igual a 12 combinaciones.`**

Se creó un archivo ejecutable **.sh** donde se pone todo lo que va a correr en el terminal, **p.e.** crear 12 carpetas de las combinaciones mencionadas anteriormente y los comandos de **denovo.map.pl** en este archivo. 

Para esto hicimos:

> macs-MacBook-Pro-2:Limno_PUCE_ddRAD macuser$ chmod +x /Users/macuser/Documents/GenomicaPUCE/MingaGenomica/Hyalella_1.sh 

> _chmod se lo puede hacer desde cualquier ubicación en mi caso lo hice desde Limno_PUCE_ddRAD_

>  macs-MacBook-Pro-2:MingaGenomica macuser$ ./Hyalella_1.sh 

## 3. Populations:

Luego de correr los `denovo_map.pl` corremos el pipeline de `Populations` de stacks.

Para esto se utilizaron los siguientes parámetros para todos los Tests utilizados con cada bicho: 
 
> -p: 1
 
> -r: 0.8
 
 `populations -P ./DeNovo_Hya_R1R2_T1 --popmap ./PopMap/PopMap_Hya_sin_rem_2.txt -O ./DeNovo_Hya_R1R2_T1/Populations_Hya_T1 -p 1  -r 0.8 --write_random_snp --vcf`
 
Donde:

> -p,--min-populations [int] — minimum number of populations a locus must be present in to process a locus.

> -r,--min-samples-per-pop [float] — minimum percentage of individuals in a population required to process a locus for that population.

Lo corrimos nuevamente con un archivo ejecutable: `.sh`, pero antes hicimos al archivo ejecutable con el comando `chmod u+x` y para verificar si es ejecuable el archivo se lo verifica con el comando: `ls -ltrh` y si sale el archivo con `-rwxr--r--@` es que si es ejecutable (debe contener una "x"). 
 
En el caso de Hyalella, hemos visto que al correr populations con un r = 0.8 nos quedamos con poquitos desde cero hasta menos de 10 Loci Kepts y VSRs dependiendo de la variación de m. Pero cuando corrimos con un r = 0.5 los LKs y los VSRs subieron a miles entonces, al parecer hay mucha variación en estas poblaciones de Hyalella.

En Andesiops vamos a escoger al parámetro m = 5; M = 6 y n = 7 donde nos dio una cantidad intermedia de SNPs y de Loci Kept que cuando comparábamos los m = 4 y m = 6, el único parametro con el que los resultados variaron de buena manera fue con m. Ahora vamos a probar combinaciones con n = 4 y n = 5.

## 3.1 Filtrando Matrices:

Primero corrimos un **`populations`** con los parámetros: -p: 1 y -r: 0.1 muy bajos para que no discrimine a los individuos pertenecientes a las diferentes localidades donde fueron colectados para no filtrar de el Test de parámetros donde obtivimos los mejores resultados de LK y VSR.

Instalamos vcftools de acuerdo a la página de la minga genómica:

> git clone https://github.com/vcftools/vcftools

Luego instalamos plink, descargandonos de la página de la minga y al .zip lo descomprimimos para luego ponerlo dentro de una carpeta donde quedará el archivo ejecutable.

Creamos un path al `.bash_profile`: 

> Primero nos fuimos a "Home Directory" con el comando `cd ~`, ahí escribimos `nano .bash_profile` y ahí creamos el path con el comando: `export PATH="/Users/macuser/Documents/GenomicaPUCE/MingaGenomica/plink-1.07-mac-intel :$PATH"`

Después seguimos con el paso de la minga haciendo:

./autogen.sh  **##Desde el directorio de vcftools**

Nos dio un error: 
> `./autogen.sh: line 3: autoreconf: command not found`

Pusimos: `brew install autoconf automake`

Luego hicimos: `./configure`

## 3.2 Filtros p-link:

Plink ayuda a remover SNPs raros (que estan en pocos individuos), individuos con pocos datos y alelos raros. Para estos analisis primero hay que:

* Mover el ejecutable de plink al directorio donde están los archivos que se van a filtrar (.ped y .map).
* Luego se filtran los datos en una serie de pasos:

1. Filtrado de Loci (Primero filtramos loci con demasiados datos que faltan, en el % que especifiquemos en el código --geno):
 `./plink --file input-name --geno 0.5 --recode --out output-filename_a --noweb`
 
 > **Resultados `Hya_T17_a`:**
 
 > **geno 0.5:**
 
  > * `After frequency and genotyping pruning, there are 4202 SNPs`
  
 
 > **Resultados:**
 
 > **geno 0.5:**
 
  > * `Hya_T17_b`: 20 of 44 individuals removed for low genotyping ( MIND > 0.5 )
  
2. Segundo, filtramos individuos con muchos datos que faltan:

`./plink --file input-filename_a --mind 0.5 --recode --out output-filename_b --noweb`
  
 
3. Minor Alelle Frecuency (Tercero, filtramos basado en frecuencia alelica menor, en el % de individuos que especifiquemos en el código --maf):
`./plink --file input-filename_b --maf 0.01 --recode --out output-filename_c --noweb`

> **Resultados:**

 > **geno 0.5**:
 
 > * `Hya_T17_c`: 289 SNPs failed frequency test ( MAF < 0.01 ). After frequency and genotyping pruning, there are 3913 SNPs
  
**Finalmente nos quedamos con los parámetros:**

> geno: 0.47; mind: 0.5 & maf: 0.01 el cual removió 19 de 44 individuos que no poseen SNPs compartidos en el 53 % de las poblaciones, los cuales son (obtenidos del file .irem):

> 1	`017Hya_G14V`
2	`018Hya_G14M`
3	`095Hya_G14M`
4	`096Hya_G14M`
5	`113Hya_G14M`
6	`118Hya_G`
7	`119Hya_G`
8	`125Hya_PurV`
9	`126Hya_PurV`
10	`127Hya_PurV`
11	`128Hya_PurV`
12	`137Hya_V`
13	`145Hya_PurgM`
14	`146Hya_PurgM`
15	`147Hya_PurgM`
16	`161Hya_M`
17	`166Hya_PurG`
18	`193Hya_M`
19	`194Hya_PurM` 

Puede que sea bueno investigar el efecto de distintos 'minor allele frequency thresholds', por lo que es bueno generar varios outputs con maf 0.01, 0.02, y 0.05, para luego ver en **_adegenet_** (ver abajo) si hay alguna relacion entre maf threshold y estructura poblacional.

Ahora, veamos que tanto 'linkage' hay en nuestros datos, con el siguiente codigo:

`./plink --file input-filename_c --r2 --out outputfilename`

Ejemplo código Vero:

`./plink --file And_T8_c --r2 --out And_T8_Link`

- Blacklist: Es necesario eliminar loci basado en el analisis? De ser el caso, crear un 'blacklist' para excluir en plink con el codigo:

`./plink --file filename --exclude LD-loci-list.txt --recode --out LD-filtered`

   * El blacklist debe estar en formato de un SNP por linea, con la identidad completa (locus mas posicion del SNP).

### Ejemplo Vero:

El input file es el archivo final de lo anterior (aplicados los tres filtros seleccionados). Con esto se obtienen varios archivos (.ld, .log .nosex). El archivo .ld inidca el nivel de linkage entre pares de SNPs y nos sirve para luego excluir SNPs con alto linkage. Para esto: 

- Abrimos el archivo .ld en TextWrangler y lo transformamos a .txt para abrirlo con Excel, donde ordenamos las filas de acuerdo al r2 que representa la proporcion de linkage.   

		Nota 1: En el archivo .ld los nombres de los SNPs estan dados como: 4432:3. El primer número representa el número de locus (Stacks asigna cualquier numero a los locus) y el segundo la posicion del SNP en ese locus.    

		Nota 2: Si excel no lee bien los nombres de las celdas (¡¡¡en nuestro caso les dio formato de fecha!!!) se puede cambiar con TextWrangler los ":" por "-".

- Elegimos un umbral de linkage y excluimos todos los SNPs que tengan un linkage mayor a este umbral. En el caso de _Andesiops_ vamos a excluir SNPs con linkage mayor a 0.6 (con > 0.5 perdemos demasiados SNPs). 

- En la columna SNP_A del excel, elegimos todos los SNPs con r2 mayor o igual a nuestro umbral. Copiamos esas celdas, pegamos en otra hoja de Excel y removemos los duplicados (boton "Remove Duplicates" de la pestaña "Data").

- Copiamos y pegamos esas celdas en un archivo de TextWrangler (cambiamos "-" por ":" de ser necesario) y lo grabamos como .txt con un nombre como "Blacklist_AndT8.txt". Este archivo se va a usar para excluir los SNPs con alto linkage en lo siguiente:

`./plink --file And_T8_c --exclude BlackList_AndT8.txt --recode --out And_T8_d` 

#### Esto nos deja con:

De lo hecho por Vero con Andesiops:

**2633** SNPs y **72** individuos para _Andesiops_ T8  

**2892** SNPs y **73** individuos para _Andesiops_ T7

De lo hecho por mi con Hyalella:

**746** SNPs y **25** individuos para _Hyalella_ T17



## Heterocigocidad

Para estimar el nivel de heterocigocidad, que nos da una idea del nivel de endogamia (inbreeding) en las poblaciones, usamos el siguiente código:

`plink --noweb --file mydata --het --out outputfilename`

`./plink --noweb --file And_T8_d --het --out And_T8_Het`

El _input file_ es el output del paso anterior, osea ya filtrado el linkage, en este caso `And_T8_d`.

Esto nos da un archivo .het el cual tiene un valor, F y que es un estimado de inbreeding basado en niveles de heterocigosidad esperada y observada.
 
## 5. Análisis de las matrices de SNPs con R
Los archivos .ped y .map que se obtienen de lo anterior se deben convertir a archivos de structure (.str). Esto se hace con el programa PGDSpider...  
Por alguna razón, el archivo .str obtenido asigna una población diferente a cada individuo, en lugar de asignarles las poblaciones del archivo popmap. El archivo se ve así:  

![ArchivoStr_SinModf](https://github.com/verocrespoperez/InvertGenomics/blob/master/Fotos/Screen_Shot_Str1.png)

Para solucionar esto se puede modificar el archivo con grep en TextWrangler de la siguiente manera:

![GrepTW2](https://github.com/riokoto80/Limno_PUCE_ddRAD/blob/master/Fotos/Screen_Shot_Grep2_Str.png)

El archivo modificado se debería ver así:  

![ArchivoStr_Modf](https://github.com/riokoto80/Limno_PUCE_ddRAD/blob/master/Fotos/Screen_Shot_Str2.png)

##QUESTIONS FOR STRUCTURE FILES:
How many genotypes are there? Número de individuos que quedaron en la matriz .stru
How many markers are there? Número de SNPs
Which column contains labels for genotypes ('0' if absent)? 
Answer:1  
Which column contains the population factor ('0' if absent)? 
Answer:2
Which other optional columns should be read (press 'return' when done)? 
Which row contains the marker names ('0' if absent)? 
Answer:1
Are genotypes coded by a single row (y/n)? Depende si es single branded or double branded

104Hya_G14M Salió como outlier y el individuo 154Hya_M un poco así:
Subir Foto PCA

Entonces se procede a eliminar el outlier fuerte, para probar y luego haremos lo mismo con el segundo outlier.


## Manejo de Github:

`macs-MacBook-Pro-2:MingaGenomica macuser$ which git`

Para saber si está instalado el Github y sale:

> /usr/local/bin/git >  **ubicación del git en la computadora**

**Resto de pasos ver en la página web de la Paty:** https://github.com/pesalerno/MingaGenomica2019/blob/master/git-minga.md

> En resumen se deben usar los comandos "git add + archivo.md", "git status (para ver si se subió el archivo correctamente, debe salir en letras de color verde)", "git commit - m 'mensaje que quieres dar a tus colaboradores o a ti mismo cuando editas el .md'" & "git push origin master (para ya subir los cambios a tu página Git)" luego haces un refresh de la página web donde esta tu Git y verás los cambios realizados.

**Para colaborar:**

**Se lo realiza mediante el comando _git pull_, siguiendo los siguientes pasos de la página de la Paty:** 
https://github.com/pesalerno/PUMAgenomics/blob/master/git-collaborating-protocol.md

Atopsyche parámetros como Andesiops (Vero)
Anomalocosmoecus como Hyalella (Redro)



