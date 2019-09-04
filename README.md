# BoundingBoxes
In bulk berekenen van bounding boxes voor gebieden in verschillende verhoudingen.

## toelichting 
Om grote datasets of bijvoorbeeld datasets van services efficient in te kunnen lezen is het raadzaam om een ruimtelijk filter toe te passen. Daarmee blijft de hoeveelheid data die verwerkt moet worden beperkt tot het aandachtsgebied.

Deze dataset geeft per gebied aan wat de uiterste coördinaten in RD (epgs:28992) zijn.

Soms is het prettig om de verhoudingen van de uitsnede overeen te laten komen met het tekencanvas. Bijvoorbeeld als er in Adobe Illustrator (icm Mappublisher) een kaart op A0-formaat gemaakt moet worden.

Deze dataset geeft daarom ook bij elk gebied aan wat de uiterste coördinaten zouden moeten zijn om het tekencanvas geheel te vullen waarbij in ieder geval alle gegevens in het aandachtsgebied geladen worden. Dus waarbij de uitsnede dezelfde hoogte/breedte-verhouding heeft als die van het tekencanvas.

De dataset geeft dat aan voor A-formaten (1:sqrt(2)). Het script kan echter ook voor elke willekeurige verhouding worden uitgevoerd.

## bronnen
* Territoriale Indeling Rotterdam (TIR)

## bewerking

Per gebied:

1. bepalen bepalen bounding box van het gebied (minX, minY, maxX, maxY)
2. bepalen afmetingen van de bounding box:
   - difX := maxX - minY
   - difY := maxY - maxY
3. bepalen verhoudingen:
   - verhoudingCanvas := korte zijde canvas / lange zijde canvas (bij A-formaat is dat 1/sqrt(2) =~0,707)
   - verhoudingGebied := difY / difX
4. berekenen gewenste uitbreiding van de boundingbox om de verhoudingen overeen te laten komen voor landscape:
   - als verhoudingCanvas = verhoudingGebied:
     - geen wijzigingen
   - als verhoudingCanvas < verhoudingGebied:
     - deltaX := difY / verhoudingCanvas) - difX
     - minX := minX - (deltaX / 2)
     - maxX := maxX + (deltaX / 2)
   - als verhoudingCanvas > verhoudingGebied:
     - deltaY := (difX * verhoudingCanvas) - difY
     - minY := minY - (deltaY / 2)
     - maxY := maxY + (deltaY / 2)
5. berekenen gewenste uitbreidingen van de boundingbox voor portrait:
   - zie stap 4, met verhoudingCanvas := 1 / verhoudingCanvas

## gebruik van het script
De command line aanroep is:
```
"C:\Program Files (x86)\FME\fme.exe" K:\GEODATA\Kennisloods\Beheer\BoundingBoxes.fmw
--CANVASLANG "1189"
--CANVASKORT "841"
--BRON_TIR_GEMEENTE_SHP "K:\GEODATA\Basisdata\TIR\Data\SHP\Gemeente\Gemeente_vlakken.shp"
--BRON_TIR_GEBIEDEN_SHP "K:\GEODATA\Basisdata\TIR\Data\SHP\Gebieden\Gebieden_vlakken.shp"
--BRON_TIR_BUURTEN_SHP "K:\GEODATA\Basisdata\TIR\Data\SHP\Buurten\Buurten_vlakken.shp"
--DOEL_CSV "K:\GEODATA\Kennisloods\Data\BoundingBoxes\CSV"
--DOEL_SHP "K:\GEODATA\Kennisloods\Data\BoundingBoxes\SHP"
--LOG_FILE "K:\GEODATA\Kennisloods\Beheer\log\BoundingBoxes.log"
```

De betekenis van de parameters is:

| parameter               | betekenis |
| ---------               | --------- |
|`CANVASLANG`             | Lange zijde van het beoogde canvas. |
| `CANVASKORT`            | Korte zijde van het beoogde canvas. |
| `BRON_TIR_GEMEENTE_SHP` |  	Bronlocatie van de gemeente in deTIR. |
| `BRON_TIR_GEBIEDEN_SHP` |  	Bronlocatie van de gebieden in deTIR. |
| `BRON_TIR_BUURTEN_SHP`  |  	Bronlocatie van de buurten in deTIR. |
| `DOEL_CSV`              |  	Folder waarheen de administratieve lijst van bounding boxes wordt weggeschreven in CSV-formaat. |
| `DOEL_SHP`              |  	Folder waarheen de geografische representatie van de bounding boxes wordt weggeschreven in ESRI Shape-formaat. |
| `LOG_FILE`              | Locatie van het logbestand. |

De lange zijde en korte zijde van A0 zijn bijvoorbeeld 1189 resp. 841. De exacte afmetingen zijn niet van belang, zolang de verhouding maar correct is. De afmetingen van A4: 297 resp. 210, geven hetzelde resultaat omdat 1189 / 841 gelijk is aan 297 / 210.

