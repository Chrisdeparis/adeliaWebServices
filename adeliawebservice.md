https://teams.microsoft.com/l/message/19:7461b650-118f-42ae-ada7-e5aadc4fcae3_8a1a2034-e8d6-438a-b81d-95bb5d28c99f@unq.gbl.spaces/1699626097745?context=%7B%22contextType%22%3A%22chat%22%7D

xpath xml filter documentation sur internet
Plugins > XML TOOLS > XPath expression
log4c/appender//@

[10/11 15:21] William LECLERCQ
w_prf_trace = '[RECHERCHE_GARE]'
 
cho_id       = *blank

cho_name     = *blank

cho_zip_code = *blank

cho_insee    = *blank
 
w_place_req = c_place_req /// lsv_depart

w_key_token = c_key_token

w_val_token = c_val_token
 
w_options = '--request GET --header Accept:application/json --header'/// ' ' // w_key_token /// ':'/// w_val_token /// ' --insecure'

si bac_debug

   w_options = w_options /// ' ' // lbr_donnees /// ' ' // lbr_reponse 

fin

executer_http *url(w_place_req) -

*options(w_options) -

*reponse(w_rep_service_json) -

*code_http(w_code_http)

si *code_retour <> 0

   cho_code_erreur = *code_retour

   traiter_proc lib_err_adelia 'executer_http' cho_code_erreur chs_lib_erreur

sinon

   choix_selon w_code_http

      si_valeur 200

         * Le retour est ok

         si bac_debug

            cho_code_erreur = w_code_http

            * Enregistrement de l'image reçue

            appeler_dll 'vatoolbx.dll' 'VaToolBxImageToFile' w_rep_service_json 'D:\Hardis\LECLERCQ William\Expert\POC Web Service\reponse.json' w_cd_ret_bin2

         fin

         * Conversion en XML

         conv_donnees w_rep_service_json 'JSON' w_rep_service_xml 'XML'

         si *code_retour <> *normal

            cho_code_erreur = *code_retour

            traiter_proc lib_err_adelia 'conv_donnees' cho_code_erreur chs_lib_erreur

         sinon

            si bac_debug

               * Enregistrement de l'image convertie

               appeler_dll 'vatoolbx.dll' 'VaToolBxImageToFile' w_rep_service_xml 'D:\Hardis\LECLERCQ William\Expert\POC Web Service\reponse.xml' w_cd_ret_bin2

            fin

            * Chargement liste résultat

            xml_ouvrir w_fic_xml *lecture *var_doc(w_rep_service_xml) 

            si *code_retour <> *normal

               cho_code_erreur = *code_retour

               traiter_proc lib_err_adelia 'xml_ouvrir' cho_code_erreur chs_lib_erreur

            sinon

               vider_lst lst_sortie

               vider_lst frm_main.lsv_depart:liste

               clv_id = *blank                       /* la liste est vide, mais pas les valeurs de colonne.

               xml_charger_lst w_fic_xml &c_place_xpath lst_sortie *nom ls_nom, *texte ls_texte

               si &nbr_elt(lst_sortie) > 0

                  lecture_lst lst_sortie

                     choix_selon ls_nom

                        si_valeur 'id'

                           si clv_id <> *blank

                              * rupture => on écrit l'élément

                              inserer_elt frm_main.lsv_depart:liste

                           fin

                           clv_id = ls_texte

                        si_valeur 'name'

                           clv_name = ls_texte

                        si_valeur 'zip_code'

                           clv_zip_code = ls_texte

                        si_valeur 'insee'

                           clv_insee = ls_texte

                        autre_valeur

                           * Rien

                     fin_choix_selon   

                  fin_lecture_lst

                  *                  inserer_elt lst_places

                  inserer_elt frm_main.lsv_depart:liste

                  cho_nbr_element = &nbr_elt(frm_main.lsv_depart:liste)

                  si cho_nbr_element > 0

                     frm_main.lsv_depart:hauteur = 26 + &nbr_elt(frm_main.lsv_depart:liste) * 13

                  sinon

                     frm_main.lsv_depart:hauteur = 20

                  fin

               fin

               xml_fermer w_fic_xml

            fin

         fin

      autre_valeur

         cho_code_erreur = w_code_http

   fin_choix_selon

fin

[10/11 15:25] William LECLERCQ
charger_dll 'vatoolbx.dll'
 
w_prf_trace = '[INIT_PGM]'
 
CR =  &conversion_car('0D')

HT =  &conversion_car('09')

LF =  &conversion_car('0A')
 
c_key_token = 'Authorization'

c_val_token = 'f00b6b71-d64e-4f48-b0af-9243a6c1b64c'

c_place_req = 'https://api.navitia.io/v1/coverage/fr-idf/places?q='
 
c_place_xpath = -

'/Root/places/self::places[embedded_type=''stop_area'']/id|' // -

'/Root/places/self::places[embedded_type=''stop_area'']/name|' // - 

'/Root/places/self::places[embedded_type=''stop_area'']/stop_area/administrative_regions/self::administrative_regions[level=8]/zip_code|' // -

'/Root/places/self::places[embedded_type=''stop_area'']/stop_area/administrative_regions/self::administrative_regions[level=6]/insee'

* Différents types de régions administrative 8=commune, 6 = département, 4 = région

* level = 8 permet de ne récupérer que les codes postaux des communes

* level = 6 permet de ne récupérer que les n° de département
 
initialiser frm_main

executer    frm_main