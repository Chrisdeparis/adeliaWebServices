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

﻿

* Gestion du fichier XML retourné en réponse de l'appel du WebService :
* Selon le code rendu par l'appel du WS, le traitement differe
si httpstatus = 200;201
#
Code retour OK - Tentative de récupération et impression etiquette
* Ouverture de 1' XML de la réponse
xml_ouvrir xml_bpost_reponse lecture *var_doc(reponse)
si *code_retour = *normal
w_trace = w_trace_bloc /// ajouter_trace w_trace *debug
sinon
OUVERTURE DU DOM XML Reponse XML - OK'
traiter_proc lib_err_adelia 'XML_OUVRIR
OUVERTURE DU DOM XML - code_retour
Reponse XML - KO'
ajouter_trace w_trace *erreur
fin
w_trace = w_trace_bloc !!!
* Définition des names spaces contenu dans le XML de réponse pour un statut OK traiter_proc decl_namespace
#
* Recherche de l'existence de la valeur pour le fichier PDF
w_xpath_pdf = '/ns1:labels/ns1:label/ns1: bytes'
xml_selection xml_bpost_reponse &w_xpath_pdf w_nb_val
si *code_retour = #normal
w_trace w_trace_bloc //!' Existence Valeur - //w_xpath_pdf /// ajouter_trace w_trace *debug
sinon
fin
w_trace = w_trace_bloc /// Existence Valeur - // w_xpath_pdf /// traiter_proc lib_err_adelia 'xml_selection' *code_retour
ajouter_trace w_trace erreur
NombreValeur = /// &num_alpha(w_nb_val)
NombreValeur = /// &num_alpha(w_nb_val) /// ` - CodeRetour = /// &num_alpha(*code_retour)
* Si il existe bien une valeur pour le XPath renseigné alors on récupère le PDF :
si w_nb_val> 0
Récupération du fichier PDF
xml_selection xml_bpost_reponse &w_xpath_pdf
w_cde_ret_selec = *code_retour
xml recup_val xml_bpost_reponse rep_label
w_cde_ret_recup = *code_retour
si w_cde_ret_recup = normal et w_cde_ret_selec = *normal
w_trace = w_trace_bloc /// Récupération de l''étiquette avec w_xpath_pdf ajouter_trace w_trace *debug
* Enregistrement du fichier dans le répertoire de destination:
* conversion de la variable image rep_label en file et enregistrement du fichier si w_contexte 'PRD ou pi_reference contient 'Est'
si w_contexte = 'PRD' et pi_reference contient 'tst'
pi chemin etiqu= dt_dbg_chemin
w_chemin etiqu= dt_dbg_chemin /// 'etiq_DBG_' // pi_reference /// '.pdf'
sinon
fin
w_chemin etiqu= pi_chemin_etiqu /// 'etiq_' // pi_reference /// '.pdf'
// w_xpath_pdf ///
appeler_dll 'vatoolbx.dll' 'VaToolBxImageToFile' rep_label w_chemin_etiqu w_nb2_coderetour / Attention: casse exacte de la fonction à respecter !!! si w_nb2_coderetour = 0 po_msg_retour = w_chemin_etiqu
```
