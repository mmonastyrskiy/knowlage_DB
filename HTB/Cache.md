Все инструменты описанные в данной статье реальны, находятся в свободном доступе и используются специалистами по Информационной Безопасности для проведения тестирования на проникновение информационных систем компаний и предприятий. Автор статьи не занимается взломом и не поощряет подобных действий, все описанное ниже было выполнено на специально подготовленной для этого виртуальной машине.

[[nmap]]
```bash
Nmap scan report for 10.10.10.188

Host is up (0.12s latency).

Not shown: 998 closed ports

PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)

| ssh-hostkey:

| 2048 a9:2d:b2:a0:c4:57:e7:7c:35:2d:45:4d:db:80:8c:f1 (RSA)

| 256 bc:e4:16:3d:2a:59:a1:3a:6a:09:28:dd:36:10:38:08 (ECDSA)

|_ 256 57:d5:47:ee:07:ca:3a:c0:fd:9b:a8:7f:6b:4c:9d:7c (ED25519)

80/tcp open http Apache httpd 2.4.29 ((Ubuntu))

|http-server-header: Apache/2.4.29 (Ubuntu)

|http-title: Cache

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

Идем смотреть на вебсайт, видим что он не очень отличается сложностью, не вывозит PHP и не трудно найти форму входа, автор на одной из страниц написал о себе, там он упоминает еще несколько проектов, свой никнейм и вроде как сливает нам доменное имя для этого сайта, редактируем **/etc/hosts**

Ничего не изменилось, поехали дальше, [[gobuster]]?

```
/news.html (Status: 200)

/login.html (Status: 200)

/index.html (Status: 200)

/contactus.html (Status: 200)
```

Не густо, ну и делать нечего, придется ломать форму входа, благо это не сложно, так как сайт вывозит только на JS все проверки подлинности происходят на компьютере юзера, а значит код можно посмотреть, и он грузится вместе с сайтом

Находим в памяти нужный файл, входим, неужели все так просто?

Кроличья нора…..  
пойдем искать дальше, он говорил, что-то о других своих проектах  
попробуем добавить их в доменное имя.

вот, сайт уже выглядит лучше, и использует PHP

для начала [[gobuster]]

```
/index.php (Status: 302)

/images (Status: 301)

/templates (Status: 301)

/services (Status: 301)

/modules (Status: 301)

/common (Status: 301)

/library (Status: 301)

/public (Status: 301)

/version.php (Status: 200)

/admin.php (Status: 200)

/portal (Status: 301)

/tests (Status: 301)

/sites (Status: 301)

/custom (Status: 301)

/javascript (Status: 301)

/contrib (Status: 301)

/interface (Status: 301)

/vendor (Status: 301)

/config (Status: 301)

/setup.php (Status: 200)

/Documentation (Status: 301)

/sql (Status: 301)

/controller.php (Status: 200)

/LICENSE (Status: 200)

/ci (Status: 301)

/cloud (Status: 301)

/ccr (Status: 301)

/patients (Status: 301)

/repositories (Status: 301)

/myportal (Status: 301)

/entities (Status: 301)

/controllers (Status: 301)

/server-status (Status: 403)
```
Уже лучше, есть с чем работать..

Стандартные SQL инъекции [[SQLi]] не особо спасли, а пока [[gobuster]] работает пойдем погуглим, что это за платформа такая, о видео с атакой на сервак на локальном хосте? Удобно. Посмотрим…

стоп… **/portal** ??? а [[gobuster]] не показывал…  
ладно, прогоним еще один словарь прежде чем продолжить

```
/.hta (Status: 403)

/.hta.php (Status: 403)

/.htaccess (Status: 403)

/.htaccess.php (Status: 403)

/.htpasswd (Status: 403)

/.htpasswd.php (Status: 403)

/admin.php (Status: 200)

/admin.php (Status: 200)

/common (Status: 301)

/config (Status: 301)

/contrib (Status: 301)

/controllers (Status: 301)

/controller.php (Status: 200)

/custom (Status: 301)

/images (Status: 301)

/index.php (Status: 302)

/index.php (Status: 302)

/interface (Status: 301)

/javascript (Status: 301)

/library (Status: 301)

/LICENSE (Status: 200)

/modules (Status: 301)

/portal (Status: 301)

/public (Status: 301)

/server-status (Status: 403)

/services (Status: 301)

/setup.php (Status: 200)

/sites (Status: 301)

/sql (Status: 301)

/templates (Status: 301)

/tests (Status: 301)

/vendor (Status: 301)

/version.php (Status: 200)
```

Теперь смотрим атаку, кажется не так сложно, верно, грабим запрос, и тестим [[sqlmap]]

Запрос из [[Burpsuit]] легко достать

```
GET /portal/add_edit_event_user.php?eid=1 HTTP/1.1

Host: hms.htb

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,/;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Connection: close

Cookie: OpenEMR=boan53s999uc0vlsvoq71u4qq8; PHPSESSID=ksm32vkbioa7obiuo9urqcs821

Upgrade-Insecure-Requests: 1
```

Находим список таблиц, нам интересна только одна, дампим её
```
Database: openemr

| array |

| groups |

| sequences |

| version |

| addresses |

| amc_misc_data |

| amendments |

| amendments_history |

| ar_activity |

| ar_session |

| audit_details |

| audit_master |

| automatic_notification |

| background_services |

| batchcom |

| billing |

| calendar_external |

| categories |

| categories_seq |

| categories_to_documents |

| ccda |

| ccda_components |

| ccda_field_mapping |

| ccda_sections |

| ccda_table_mapping |

| chart_tracker |

| claims |

| clinical_plans |

| clinical_plans_rules |

| clinical_rules |

| clinical_rules_log |

| code_types |

| codes |

| codes_history |

| config |

| config_seq |

| customlists |

| dated_reminders |

| dated_reminders_link |

| direct_message_log |

| documents |

| documents_legal_categories |

| documents_legal_detail |

| documents_legal_master |

| drug_inventory |

| drug_sales |

| drug_templates |

| drugs |

| eligibility_response |

| eligibility_verification |

| employer_data |

| enc_category_map |

| erx_drug_paid |

| erx_narcotics |

| erx_rx_log |

| erx_ttl_touch |

| esign_signatures |

| extended_log |

| external_encounters |

| external_procedures |

| facility |

| facility_user_ids |

| fee_sheet_options |

| form_care_plan |

| form_clinical_instructions |

| form_dictation |

| form_encounter |

| form_eye_mag |

| form_eye_mag_dispense |

| form_eye_mag_impplan |

| form_eye_mag_orders |

| form_eye_mag_prefs |

| form_eye_mag_wearing |

| form_functional_cognitive_status |

| form_group_attendance |

| form_groups_encounter |

| form_misc_billing_options |

| form_observation |

| form_reviewofs |

| form_ros |

| form_soap |

| form_taskman |

| form_vitals |

| forms |

| gacl_acl |

| gacl_acl_sections |

| gacl_acl_seq |

| gacl_aco |

| gacl_aco_map |

| gacl_aco_sections |

| gacl_aco_sections_seq |

| gacl_aco_seq |

| gacl_aro |

| gacl_aro_groups |

| gacl_aro_groups_id_seq |

| gacl_aro_groups_map |

| gacl_aro_map |

| gacl_aro_sections |

| gacl_aro_sections_seq |

| gacl_aro_seq |

| gacl_axo |

| gacl_axo_groups |

| gacl_axo_groups_map |

| gacl_axo_map |

| gacl_axo_sections |

| gacl_groups_aro_map |

| gacl_groups_axo_map |

| gacl_phpgacl |

| geo_country_reference |

| geo_zone_reference |

| globals |

| gprelations |

| history_data |

| icd10_dx_order_code |

| icd10_gem_dx_10_9 |

| icd10_gem_dx_9_10 |

| icd10_gem_pcs_10_9 |

| icd10_gem_pcs_9_10 |

| icd10_pcs_order_code |

| icd10_reimbr_dx_9_10 |

| icd10_reimbr_pcs_9_10 |

| icd9_dx_code |

| icd9_dx_long_code |

| icd9_sg_code |

| icd9_sg_long_code |

| immunization_observation |

| immunizations |

| insurance_companies |

| insurance_data |

| insurance_numbers |

| issue_encounter |

| issue_types |

| lang_constants |

| lang_custom |

| lang_definitions |

| lang_languages |

| layout_group_properties |

| layout_options |

| lbf_data |

| lbt_data |

| list_options |

| lists |

| lists_touch |

| log |

| log_comment_encrypt |

| log_validator |

| medex_icons |

| medex_outgoing |

| medex_prefs |

| medex_recalls |

| misc_address_book |

| module_acl_group_settings |

| module_acl_sections |

| module_acl_user_settings |

| module_configuration |

| modules |

| modules_hooks_settings |

| modules_settings |

| multiple_db |

| notes |

| notification_log |

| notification_settings |

| onotes |

| onsite_documents |

| onsite_mail |

| onsite_messages |

| onsite_online |

| onsite_portal_activity |

| onsite_signatures |

| openemr_module_vars |

| openemr_modules |

| openemr_postcalendar_categories |

| openemr_postcalendar_events |

| openemr_postcalendar_limits |

| openemr_postcalendar_topics |

| openemr_session_info |

| patient_access_offsite |

| patient_access_onsite |

| patient_birthday_alert |

| patient_data |

| patient_portal_menu |

| patient_reminders |

| patient_tracker |

| patient_tracker_element |

| payment_gateway_details |

| payments |

| pharmacies |

| phone_numbers |

| pma_bookmark |

| pma_column_info |

| pma_history |

| pma_pdf_pages |

| pma_relation |

| pma_table_coords |

| pma_table_info |

| pnotes |

| prescriptions |

| prices |

| procedure_answers |

| procedure_order |

| procedure_order_code |

| procedure_providers |

| procedure_questions |

| procedure_report |

| procedure_result |

| procedure_type |

| product_registration |

| product_warehouse |

| registry |

| report_itemized |

| report_results |

| rule_action |

| rule_action_item |

| rule_filter |

| rule_patient_data |

| rule_reminder |

| rule_target |

| shared_attributes |

| standardized_tables_track |

| supported_external_dataloads |

| syndromic_surveillance |

| template_users |

| therapy_groups |

| therapy_groups_counselors |

| therapy_groups_participant_attendance |

| therapy_groups_participants |

| transactions |

| user_settings |

| users |

| users_facility |

| users_secure |

| valueset |

| voids |

| x12_partners |

о, users_secure выглядит интересно…  
дампим…

+------+--------------------------------+---------------+--------------------------------------------------------------+---------------------+---------------+---------------+-------------------+-------------------+

| id | salt | username | password | last_update | salt_history1 | salt_history2 | password_history1 | password_history2 |

+------+--------------------------------+---------------+--------------------------------------------------------------+---------------------+---------------+---------------+-------------------+-------------------+

| 1 | salt| login| Hash**. | 2019-11-21 06:38:40 | NULL | NULL | NULL | NULL |

+------+--------------------------------+---------------+--------------------------------------------------------------+---------------------+---------------+---------------+-------------------+-------------------+
```
по моему то, что нужно, крякаем хэш джоном или другой тулзой и входим

Функционала много, толку мало, файлы с шелл кодом грузануть не дает…

пойду гуглить сплоиты

то что нужно, да еще и на python`e  
а если попробую….

о, www-data  
ладно, а если использовать старые данные для учетки, о, еще и user.txt можно сдать, круто

Только вот дальше дикая дичь, никогда такого не видел чтобы все что я юзал, не помогало ВАЩЕ НИКАК.

Признаюсь честно, подсмотрел, что надо посмотреть в [[netstat]], вот правда, больше не было идей, там был сервис на стременном порту, из него через [[telnet]] можно достать логин и пароль для второго юзера и зайти под ним, он конечно не root, но уже куда-то с места двинулись.

правда ход дальше был очевиден, посмотрев в id сразу можно было понять вектор следующей атаки id показывает, что мы член группы [[docker]], а это так-то не плохо. на [[Вебсайты#GTFOBins]] есть подходящая атака, простым выполнением которой нам удается получить root шелл.

Хоть и пришлось попотеть, но ROOTED