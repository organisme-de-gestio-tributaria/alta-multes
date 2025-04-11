# Webservice per ajuntaments d'alta d'infraccions de trànsit

Aquest webservice permet donar d’alta multes i obtenir els fitxers mestres de suport per introduir-les. Aquest servei es va pensar inicialment per fer-se servir des de dispositius de tipus PDA. Cada dispositiu ve identificat per l'IMEI de la PDA, que està associat a un Ajuntament. Amb el web service es poden demanar rangs de números d'expedient dins d'aquell Ajuntament, per poder identificar unívocament la multa. És a dir, la multa s'identifica pel codi d'Ajuntament i número d'expedient. Com que cada PDA té el seu rang de números expedient, es pot treballar sense connexió mentre no s'exhaureixi aquest rang de números. Aquest web service, però, també pot ser cridat des de:
* Una aplicació per a telèfon mòbil. En aquest cas, cada telèfon mòbil treballarà amb un Ajuntament concret, i s'identificarà de cara a l'ORGT com si fos una PDA "virtual". Per tant, aquest codi que diem IMEI en realitat serà un codi assignat per l'ORGT per cada telèfon mòbil, de forma que sigui únic. Si el telèfon mòbil es vol fer servir per treballar amb diversos Ajuntaments, per cadascun d'ells haurà d'identificar-se de cara a l'ORGT amb un IMEI diferent que li assignarà l'ORGT.
* Una aplicació web centralitzada. En aquest cas, o bé tota l'aplicació centralitzada gestiona un Ajuntament, i per tant s'identifica de cara a l'ORGT amb un únic IMEI, o bé gestiona diversos Ajuntaments, i per tant s'identifica per cada Ajuntament amb un IMEI diferent. En qualsevol dels dos casos, l'IMEI també l'assignarà l'ORGT seguint una nomenclatura única, que serà del tipus nom_empresa-codi_ajuntament, on nom_empresa és l'empresa que dóna el servei centralitzat, i codi_ajuntament és el codi ORGT de l'Ajuntament. Per exemple, si l'empresa "Multa S.A." gestiona l'Ajuntament d'Abrera, el seu IMEI serà "MULTA-001".


Cal accedir al webservice mitjançat l'endpoint REST. L’especificació es pot obtenir del [fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/swagger%20WcfMultesPDA.json).

El servei es troba a les següents URL's:
* Producció: https://pda.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/
* Pre-produció (proves): https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/

Aspectes importants a tenir en compte:
1. L’accés al web service serà via https i amb autenticació per certificat, tant per la part client com per la part de servidor. 
1. **Tant les peticions en mode POST com les respostes són en XML.**
1. Es poden fer proves d'altes de multes a producció, per provar la connectivitat amb aquest entorn. Només cal posar un codi d'agent que comenci amb "USU", com per exemple "USU1". Totes les multes amb aquests codis d'agent es consideren proves, i per tant no entren al sistema de Multes de l'ORGT.
1. **Previ a les proves cal comunicar el certificat utilitzat a l’ORGT ja que és necessari instal·lar la clau pública als servidors de la ORGT.** Vegeu el procés de sol·licitud a la [pàgina principal](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria)

L’explicació de cada funció del servei i de les dades que demana es troba a:
* [Fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/swagger%20WcfMultesPDA.json). 
* [Comentaris del WSDL](https://pda.orgt.diba.cat/WcfMultesPDA/svcMultesPDA.svc?singleWsdl). Podeu cancel·lar la sol·licitud de certificat per a accedir al WSDL. Malgrat que aquest wsdl està disponible només per la versió SOAP, els noms dels camps i les explicacions són les mateixes que per la versió REST.
* L’esquema de validació de les dades rebudes es troba en el mateix webservice; en el cas de proves a: https://pdaprv16.orgt.diba.cat/RestMultesPDA/schema/svcMultesPDA.xsd 

El webservice ofereix les següents operacions:
1. AltaMulta: dóna d'alta una multa.
1. AltaAnulada: anul·la una multa prèviament entrada.
1. Consulta: Consulta d'una multa. La multa s'identifica pel codi de municipi i el número d'expedient dins d'aquell municipi.
1. AltaInci: dóna d'alta una incidència a la via pública.
1. ObtenirActualitzacions: obté les darreres actualitzacions (a partir d'una data donada) dels fitxers mestres de suport, així com dels documents associats.
1. ObtenirRang: obté un rang de números d'expedient per assignar a les multes a donar d'alta.
1. ObtenirMunicipis: Obté el llistat complet de municipis amb el corresponent codi ORGT i el codi INE de 10 dígits.
1. Existeixen altres funcions però estan obsoletes

## Requisits

Abans de fer servir el web service, cal:
* Cal realitzar el procés de sol·licitud d'adhesió al webservice, [el seu detall està disponible a la pàgina principal](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria). Com a part d'aquest procés, cal facilitar la part pública del certificat amb el qual s'accedirà al web service. El mateix certificat servirà tant per accedir a proves com a producció. Aquest certificat és necessari tant per cridar el web service com per accedir a l'especificació (wsdl, ajuda REST i schema).
* Comunicar a l'ORGT els dispositius que es faran servir, per assignar-los un IMEI i donar-los d'alta a la nostra base de dades. Aquesta comunicació s'ha de fer també cada vegada que s'incorpora un nou dispositiu. 
* Ja no cal comunicar els nous agents, sempre i quan a l'alta de la multa s'especifiqui el camp "quiden", indicant si qui fa la denúncia és un agent ("A"), un vigilant ("V"), un particular ("P") o un altre tipus de persona ("X"). 



## Exemples de crides i respostes

A continuació es presenten diversos exemples de crides i respostes. Podeu trobar més informació a:
* **[Especificació swagger](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/swagger%20WcfMultesPDA.json). Podeu crear el vostre client de forma automàtica a partir d'aquest fitxer.**
* [Comentaris del WSDL](https://wsproves.orgt.diba.cat/deutes/DeutesServiceREST.svc?singleWsdl). Podeu cancel·lar la sol·licitud de certificat a l'accedir al WSDL. Malgrat que aquest wsdl està disponible només per la versió SOAP, els noms dels camps i les explicacions són les mateixes que per la versió REST.
* L’esquema de validació de les dades rebudes es troba en el mateix webservice a: https://wsproves.orgt.diba.cat/deutes/schema/Deutes.xsd 
* Cal notar que totes les dades de tipus text han d'estar en majúscules.

| Endpoint | Mètode HTTP | Exemples |
|---|---|---|
| AltaMulta | POST | [URL](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/AltaMulta) <br> [Exemple XML petició](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/AltaMulta%20peticio.xml) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/AltaMulta%20resposta.xml)
| AltaAnulada | GET | [URL petició](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/AltaAnulada?pCdclie=123&pCdexpa=456&pIMEI=789) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/AltaAnulada%20resposta.xml)
| Consulta | GET | [URL petició](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/Consulta?pCdclie=123&pCdexpa=456) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/Consulta%20resposta.xml)
| AltaInci | POST | [URL](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/AltaInci) <br> [Exemple XML petició](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/AltaInci%20peticio.xml) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/AltaInci%20resposta.xml)
| ObtenirActualitzacions | GET | [URL petició](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/ObtenirActualitzacions?pIMEI=123&pClient=456&pData=202503010101&pSeguent=) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/ObtenirActualitzacions%20resposta.xml)
| ObtenirRang | GET | [URL petició](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/ObtenirRang?pImei=123&pEstat1=1&pEstat2=1) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/ObtenirRang%20resposta.xml)
| ObtenirMunicipis | GET | [URL petició](https://pdaprv16.orgt.diba.cat/RestMultesPDA/svcMultesPDA.svc/rest/ObtenirMunicipis) <br> [Resposta XML](https://github.com/organisme-de-gestio-tributaria/alta-multes/blob/main/Exemples/ObtenirMunicipis%20resposta.xml)
