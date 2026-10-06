








Hur mycket ett zoomklick ändrar nivån
När kartan skapas, ändra:
const map=L.map('map').setView([62.2,16.4],5);

till exempel till:
const map=L.map('map',{
  zoomSnap:0.25,
  zoomDelta:0.25
}).setView([62.2,16.4],5);

Det gör zoomen mycket mjukare.
- zoomDelta: 1 = standard, ganska stora hopp
- zoomDelta: 0.5 = halva steg
- zoomDelta: 0.25 = ännu finare steg
Jag skulle välja 0.25 eller 0.5.
2. När du väljer bara en OLS
Problemet är den här delen:
if(vis.length) map.fitBounds(vis,{padding:[30,30]});

När det bara finns en punkt försöker fitBounds zooma väldigt långt in.
Byt den till:
if(vis.length===1){
  map.setView(vis[0], 13);
}
else if(vis.length>1){
  map.fitBounds(vis,{padding:[30,30]});
}

Då blir beteendet:
- 1 vald OLS → zoomnivå 13
- flera OLS → kartan zoomar automatiskt så alla ryms
Du kan justera 13:
- 11 = ganska utzoomat
- 12 = lagom regionalt
- 13 = bra detaljnivå
- 14–15 = nära
- 16+ = väldigt nära
För din karta skulle jag nog sätta 12.
Så kombinationen jag rekommenderar är:
const map=L.map('map',{
  zoomSnap:0.25,
  zoomDelta:0.25
}).setView([62.2,16.4],5);

och:
if(vis.length===1){
  map.setView(vis[0], 12);
}
else if(vis.length>1){
  map.fitBounds(vis,{padding:[30,30]});
}

Det borde ge betydligt bättre känsla.






    








vart hittar jag den delen i koden





 






I ols.html.
Första stället: kartans grundinställning
Öppna ols.html på GitHub och välj Edit. Sök med Ctrl+F efter:
const map=L.map('map').setView([62.2,16.4],5);

I senaste versionen ligger den ungefär mitt i <script>-delen, strax efter att listan med ansvariga/OLS byggts upp.
Byt den raden mot:
const map=L.map('map',{
  zoomSnap:0.25,
  zoomDelta:0.25
}).setView([62.2,16.4],5);

Andra stället: automatisk zoom när OLS väljs
Sök sedan efter:
if(vis.length) map.fitBounds(vis,{padding:[30,30]});

Den ligger inne i funktionen:
function redraw(){

Byt den raden mot:
if(vis.length===1){
  map.setView(vis[0],12);
}
else if(vis.length>1){
  map.fitBounds(vis,{padding:[30,30]});
}

Sedan Commit changes.
Jag skulle börja med:
- zoomDelta: 0.25
- singel-OLS: 12
Det bör kännas betydligt mjukare.
