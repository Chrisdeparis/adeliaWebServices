pocextranet : jamais de modification de fichier environnement de référence pour déployer
juste compiler des objets

environnement de travail c'est l'environnement de correction qui est dans PEXTRANT_COR

pour pouvoir créer un programme il faut une correction.
j'ai créé ma correction et je l'ai mise par défaut.

créer un webservice qui affiche la température dans une ville

# Webservices méteo

J'utilise Geocoding : pour déterminer la longitude et latitude de la ville

puis Open-meteo pour obtenir la température à partir de la longitude et latitude.

- toujours verifier les url bien correctes avec l'objet à récupérer
- https://geocoding-api.open-meteo.com/v1/search?name=paris&count=1&language=fr
- qui récupère la latitude et longitude
- https://api.open-meteo.com/v1/forecast?latitude=43.297&longitude=5.3811&hourly=temperature_2m
- qui indique la température


transformer json en xml
filtrer avec xpath