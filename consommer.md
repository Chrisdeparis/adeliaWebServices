Pour consommer

utiliser http_executer

```sql
﻿

* Gestion de la requête HTTP et appel du WebService (via param) :

* Composition de l'URL
w_requete_url = 'https://api-parcel.bpost.be/services/shm/' // pi_accountId /// '/orders/' /// pi_reference /// '/labels/A4' /* A PARAMETRER */

w_trace = w_trace_bloc /// ' Appel du WS: '// w_requete_url /// ''

ajouter_trace w_trace *debug

* Alimentation des options
options_http = '--request GET'
options_http = options_http /// ' --insecure'                                                                                /* A PARAMETRER */
options_http = options_http /// ' --header Authorization: Basic ' // pi_cle_auth                                             /* A PARAMETRER */
options_http = options_http /// ' --header Content-type: application/vnd.bpost.shm-label Request-v3+XML'                     /* A PARAMETRER */
options_http = options_http /// ' --header Accept: application/vnd.bpost.shm-label-pdf-v3+XML'
w_trace = w_trace_bloc /// ' options du WS: ' // options_http /// ''
ajouter_trace w_trace *debug


  
* Appel du Web Service BPost :

executer_http_ *url (w_requete_url) *options (options_http) *header (1st_header) *reponse (reponse) *code_http (httpstatus) traiter_proc log_reponse_ws
traiter_proc log_reponse_ws
```
