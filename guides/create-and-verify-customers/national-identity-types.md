# National identity types

### Individual KYC

When submitting KYC data through the Fern API, you must provide the correct `nationalIdType` for the customer's `nationalIdIssuingCountry`. Each country has specific tax identification and national ID number types that are accepted for verification.

### Business KYB

When submitting KYB data for businesses, each associated person (beneficial owners, controllers, directors) must provide their `nationalIdType` matching their `nationalIdIssuingCountry` using the individual identity types below. Additionally, businesses must provide a `businessRegistrationNumber` using the business identity types listed in the separate business table.

### Individual Identity Types by Country

| Country                          | ISO 2-char | Identity Type | Description                                             |
| -------------------------------- | ---------- | ------------- | ------------------------------------------------------- |
| Afghanistan                      | AF         | `tin`         | Tax Identification Number                               |
| Albania                          | AL         | `tin`         | Tax Identification Number                               |
| Algeria                          | DZ         | `nif`         | Número de Identificação Fiscal                          |
| Angola                           | AO         | `nif`         | Número de Identificação Fiscal                          |
| Argentina                        | AR         | `cuil`        | Código Único de Identificación Laboral                  |
|                                  |            | `cdi`         | Código de Identificación                                |
| Armenia                          | AM         | `tin`         | Tax Identification Number                               |
| Australia                        | AU         | `tfn`         | Tax File Number                                         |
| Austria                          | AT         | `si`          | Social Insurance Number                                 |
| Azerbaijan                       | AZ         | `voen`        | State Taxpayer Identification Number                    |
| Bahrain                          | BH         | `cpr`         | Central Population Registry Number                      |
| Bangladesh                       | BD         | `tin`         | Tax Identification Number                               |
| Barbados                         | BB         | `nrn`         | National Registration Number                            |
| Belgium                          | BE         | `nrn`         | National Register Number                                |
| Bolivia                          | BO         | `nit`         | Número de Identificación Tributaria                     |
| Bosnia and Herzegovina           | BA         | `jmbg`        | Unique Master Citizen Number                            |
| Botswana                         | BW         | `tin`         | Tax Identification Number                               |
| Brazil                           | BR         | `cpf`         | Cadastro de Pessoas Físicas                             |
| Bulgaria                         | BG         | `ucn`         | Unified Civil Number                                    |
| Cambodia                         | KH         | `tin`         | Tax Identification Number                               |
| Cameroon                         | CM         | `nif`         | Número de Identificação Fiscal                          |
| Canada                           | CA         | `sin`         | Social Insurance Number                                 |
| Chile                            | CL         | `rut`         | Registro Único Tributario                               |
| China                            | CN         | `ricn`        | Resident Identity Card Number                           |
| Christmas Island                 | CX         | `tfn`         | Tax File Number                                         |
| Colombia                         | CO         | `nit`         | Número de Identificación Tributaria                     |
| Comoros                          | KM         | `nif`         | Número de Identificação Fiscal                          |
| Costa Rica                       | CR         | `tin`         | Tax Identification Number                               |
| Cote d'Ivoire                    | CI         | `nif`         | Número de Identificação Fiscal                          |
| Croatia                          | HR         | `oib`         | Personal Identification Number                          |
| Curaçao                          | CW         | `crib`        | Chamber of Commerce Registration Number                 |
| Cyprus                           | CY         | `tin`         | Tax Identification Number                               |
| Czech Republic                   | CZ         | `rc`          | Residence Code Number                                   |
| Denmark                          | DK         | `cpr`         | Central Person Register Number                          |
| Dominica                         | DM         | `tin`         | Tax Identification Number                               |
| Dominican Republic               | DO         | `tin`         | Tax Identification Number                               |
| Ecuador                          | EC         | `ruc`         | Registro Único de Contribuyentes                        |
| Egypt                            | EG         | `tin`         | Tax Identification Number                               |
| El Salvador                      | SV         | `nit`         | Número de Identificación Tributaria                     |
| Estonia                          | EE         | `ik`          | Individual Code                                         |
| Ethiopia                         | ET         | `tin`         | Tax Identification Number                               |
| Fiji                             | FJ         | `tin`         | Tax Identification Number                               |
| Finland                          | FI         | `hetu`        | Finnish Personal Identity Code                          |
| France                           | FR         | `spi`         | Social Security Number                                  |
| Georgia                          | GE         | `tin`         | Tax Identification Number                               |
| Germany                          | DE         | `steuer_id`   | Steueridentifikationsnummer (Tax Identification Number) |
| Ghana                            | GH         | `tin`         | Tax Identification Number                               |
| Gibraltar                        | GI         | `crc`         | Company Registration Number                             |
| Greece                           | GR         | `aom`         | Αριθμός Μητρώου (Social Security Number)                |
| Guatemala                        | GT         | `nit`         | Número de Identificación Tributaria                     |
| Haiti                            | HT         | `nif`         | Número de Identificação Fiscal                          |
| Honduras                         | HN         | `rtn`         | Registro Tributario Nacional                            |
| Hong Kong                        | HK         | `hkid`        | Hong Kong Identity Card Number                          |
| Hungary                          | HU         | `tin`         | Tax Identification Number                               |
| Iceland                          | IS         | `tin`         | Tax Identification Number                               |
| India                            | IN         | `pan`         | Permanent Account Number                                |
| Indonesia                        | ID         | `npwp`        | Nomor Pokok Wajib Pajak                                 |
| Iraq                             | IQ         | `tin`         | Tax Identification Number                               |
| Ireland                          | IE         | `ppsn`        | Personal Public Service Number                          |
| Isle of Man                      | IM         | `nino`        | National Insurance Number                               |
| Israel                           | IL         | `tin`         | Tax Identification Number                               |
| Italy                            | IT         | `cf`          | Codice Fiscale (Tax Code)                               |
| Jamaica                          | JM         | `trn`         | Taxpayer Registration Number                            |
| Japan                            | JP         | `mn`          | My Number (Individual Number)                           |
| Jordan                           | JO         | `tin`         | Tax Identification Number                               |
| Kazakhstan                       | KZ         | `iin`         | Individual Identification Number                        |
| Kenya                            | KE         | `pin`         | Personal Identification Number                          |
| Korea, Republic of               | KR         | `rrn`         | Resident Registration Number                            |
| Kuwait                           | KW         | `tin`         | Tax Identification Number                               |
| Kyrgyzstan                       | KG         | `inn`         | Individual Taxpayer Number                              |
| Lao People's Democratic Republic | LA         | `tin`         | Tax Identification Number                               |
| Latvia                           | LV         | `pk`          | Person's Code                                           |
| Liberia                          | LR         | `tin`         | Tax Identification Number                               |
| Lithuania                        | LT         | `ak`          | Personal Code                                           |
| Luxembourg                       | LU         | `matricule`   | Matricule Number (Social Security Number)               |
| Macedonia                        | MK         | `embg`        | Unique Identification Number                            |
| Madagascar                       | MG         | `nif`         | Número de Identificação Fiscal                          |
| Malawi                           | MW         | `tpin`        | Taxpayer Identification Number                          |
| Malaysia                         | MY         | `itr`         | Income Tax Reference Number                             |
| Malta                            | MT         | `tin`         | Tax Identification Number                               |
| Mauritania                       | MR         | `nif`         | Número de Identificação Fiscal                          |
| Mauritius                        | MU         | `nicn`        | National Identity Card Number                           |
| Mexico                           | MX         | `rfc`         | Registro Federal de Contribuyentes                      |
| Moldova                          | MD         | `idnp`        | Identification Number of the Person                     |
| Montenegro                       | ME         | `jmbg`        | Unique Master Citizen Number                            |
| Morocco                          | MA         | `if`          | Identification Fiscal                                   |
| Mozambique                       | MZ         | `nuit`        | Número Único de Identificação Tributária                |
| Namibia                          | NA         | `tin`         | Tax Identification Number                               |
| Nepal                            | NP         | `pan`         | Permanent Account Number                                |
| Netherlands                      | NL         | `bsn`         | Burgerservicenummer (Citizen Service Number)            |
| New Zealand                      | NZ         | `ird`         | Inland Revenue Department Number                        |
| Nicaragua                        | NI         | `ruc`         | Registro Único de Contribuyentes                        |
| Nigeria                          | NG         | `tin`         | Tax Identification Number                               |
|                                  |            | `nin`         | National Identification Number                          |
|                                  |            | `bvn`         | Bank Verification Number                                |
| Norway                           | NO         | `fn`          | Fødselsnummer (Personal Identification Number)          |
| Oman                             | OM         | `tin`         | Tax Identification Number                               |
| Pakistan                         | PK         | `ntn`         | National Tax Number                                     |
| Panama                           | PA         | `ruc`         | Registro Único de Contribuyentes                        |
| Paraguay                         | PY         | `ruc`         | Registro Único de Contribuyentes                        |
| Peru                             | PE         | `ruc`         | Registro Único de Contribuyentes                        |
| Philippines                      | PH         | `tin`         | Tax Identification Number                               |
| Poland                           | PL         | `pesel`       | Personal Identification Number                          |
| Portugal                         | PT         | `nif`         | Número de Identificação Fiscal                          |
| Puerto Rico                      | PR         | `ssn`         | Social Security Number                                  |
| Qatar                            | QA         | `qid`         | Qatar ID                                                |
| Romania                          | RO         | `cnp`         | Cod Numeric Personal                                    |
| Rwanda                           | RW         | `tin`         | Tax Identification Number                               |
| Saint Lucia                      | LC         | `tin`         | Tax Identification Number                               |
| Saint Vincent and the Grenadines | VC         | `tin`         | Tax Identification Number                               |
| Saudi Arabia                     | SA         | `tin`         | Tax Identification Number                               |
|                                  |            | `rp`          | Iqama (Residency Permit)                                |
| Senegal                          | SN         | `tin`         | Tax Identification Number                               |
| Serbia                           | RS         | `jmbg`        | Unique Master Citizen Number                            |
| Seychelles                       | SC         | `tin`         | Tax Identification Number                               |
| Singapore                        | SG         | `nric`        | National Registration Identity Card                     |
|                                  |            | `fin`         | Foreign Identification Number                           |
| Slovakia                         | SK         | `rc`          | Rodné Číslo (Personal Identification Number)            |
| Slovenia                         | SI         | `tin`         | Tax Identification Number                               |
| Somalia                          | SO         | `tin`         | Tax Identification Number                               |
| South Africa                     | ZA         | `itr`         | Income Tax Reference Number                             |
| Spain                            | ES         | `nif`         | Número de Identificación Fiscal                         |
|                                  |            | `nie`         | Número de Identificación de Extranjeros                 |
| Sri Lanka                        | LK         | `nic`         | National Identity Card Number                           |
| Suriname                         | SR         | `tin`         | Tax Identification Number                               |
| Sweden                           | SE         | `tin`         | Tax Identification Number                               |
| Switzerland                      | CH         | `avs`         | AHV Number                                              |
|                                  |            | `ahv`         | Old Age and Survivors Insurance Number                  |
| Taiwan                           | TW         | `tin`         | Tax Identification Number                               |
| Tanzania                         | TZ         | `tin`         | Tax Identification Number                               |
| Thailand                         | TH         | `tin`         | Tax Identification Number                               |
| Togo                             | TG         | `nif`         | Número de Identificação Fiscal                          |
| Trinidad and Tobago              | TT         | `bir`         | Business Identification Number                          |
| Tunisia                          | TN         | `mf`          | Matricule Fiscal                                        |
| Turkey                           | TR         | `tckn`        | Turkish Citizenship Number                              |
| Uganda                           | UG         | `tin`         | Tax Identification Number                               |
| Ukraine                          | UA         | `rnokpp`      | Registration Number of the Taxpayer                     |
| United Arab Emirates             | AE         | `emirates_id` | National Identity Card                                  |
| United Kingdom                   | GB         | `nino`        | National Insurance Number                               |
|                                  |            | `utr`         | Unique Taxpayer Reference Number                        |
| United States                    | US         | `ssn`         | Social Security Number                                  |
|                                  |            | `itin`        | Individual Taxpayer Identification Number               |
| Uruguay                          | UY         | `rut`         | Registro Único Tributario                               |
| Uzbekistan                       | UZ         | `inn`         | Individual Identification Number                        |
| Venezuela                        | VE         | `rif`         | Registro de Información Fiscal                          |
| Vietnam                          | VN         | `mst`         | Mã Số Thuế (Tax Code)                                   |
| Yemen                            | YE         | `tin`         | Tax Identification Number                               |
| Zambia                           | ZM         | `tpin`        | Taxpayer Identification Number                          |
| Zimbabwe                         | ZW         | `tin`         | Tax Identification Number                               |

### Business Registration Types by Country

For businesses, use the following registration number types for the `businessRegistrationNumber` field:

| Country                          | ISO 2-char | Registration Type | Description                                                                                     |
| -------------------------------- | ---------- | ----------------- | ----------------------------------------------------------------------------------------------- |
| Aruba                            | AW         | `cn`              | Census Number                                                                                   |
| Afghanistan                      | AF         | `tin`             | Tax Identification Number                                                                       |
| Albania                          | AL         | `nipt`            | Numri i Identifikimit për Personin e Tatueshëm                                                  |
| Andorra                          | AD         | `nrt`             | Número de Registre Tributari                                                                    |
| Angola                           | AO         | `nif`             | Número de Identificação Fiscal                                                                  |
| Argentina                        | AR         | `cuit`            | Clave Única de Identificación Tributaria                                                        |
| Armenia                          | AM         | `tin`             | Tax Identification Number                                                                       |
| Australia                        | AU         | `abn`             | Australian Business Number                                                                      |
|                                  |            | `acn`             | Australian Company Number                                                                       |
|                                  |            | `arbn`            | Australian Registered Body Number                                                               |
| Austria                          | AT         | `fn`              | Firmenbuchnummer                                                                                |
|                                  |            | `uid`             | Umsatzsteuer-Identifikationsnummer                                                              |
| Azerbaijan                       | AZ         | `voen`            | Vergi Ödəyicisinin Eyniləşdirmə Nömrəsi                                                         |
| Bahamas                          | BS         | `tin`             | Tax Identification Number                                                                       |
| Bahrain                          | BH         | `crn`             | Company/Commercial Registration Number                                                          |
| Bangladesh                       | BD         | `bin`             | Business Identification Number                                                                  |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Barbados                         | BB         | `tin`             | Tax Identification Number                                                                       |
| Belgium                          | BE         | `bce`             | Banque-Carrefour des Entreprises                                                                |
|                                  |            | `kbo`             | Kruispuntbank van Ondernemingen                                                                 |
| Belize                           | BZ         | `tin`             | Tax Identification Number                                                                       |
| Bermuda                          | BM         | `crn`             | Company/Commercial Registration Number                                                          |
| Bolivia                          | BO         | `nit`             | Número de Identificación Tributaria                                                             |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Bosnia and Herzegovina           | BA         | `id_broj`         | ID Broj (Identification Number)                                                                 |
| Botswana                         | BW         | `crn`             | Company/Commercial Registration Number                                                          |
| Brazil                           | BR         | `cnpj`            | Cadastro Nacional da Pessoa Jurídica                                                            |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Bulgaria                         | BG         | `uic`             | Unified Identification Code                                                                     |
| Cambodia                         | KH         | `tin`             | Tax Identification Number                                                                       |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Cameroon                         | CM         | `nif`             | Numéro d'Identification Fiscale                                                                 |
|                                  |            | `rccm`            | Registre du Commerce et du Crédit Mobilier                                                      |
| Canada                           | CA         | `bin`             | Business Identification Number                                                                  |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Cayman Islands                   | KY         | `crn`             | Company/Commercial Registration Number                                                          |
| Chile                            | CL         | `rut`             | Registro Único Tributario                                                                       |
| China                            | CN         | `usc`             | Unified Social Credit Code                                                                      |
| Christmas Island                 | CX         | `abn`             | Australian Business Number                                                                      |
| Colombia                         | CO         | `nit`             | Número de Identificación Tributaria                                                             |
|                                  |            | `rut`             | Registro Único Tributario                                                                       |
| Comoros                          | KM         | `nif`             | Numéro d'Identification Fiscale                                                                 |
| Costa Rica                       | CR         | `cedula_juridica` | Cédula Jurídica                                                                                 |
| Cote d'Ivoire                    | CI         | `nif`             | Numéro d'Identification Fiscale                                                                 |
| Croatia                          | HR         | `oib`             | Osobni Identifikacijski Broj (Personal Identification Number)                                   |
| Curaçao                          | CW         | `crib`            | Central Registratie en Informatiebureau                                                         |
| Cyprus                           | CY         | `crn`             | Company/Commercial Registration Number                                                          |
| Czech Republic                   | CZ         | `ico`             | Identifikační Číslo Organizace (Organization Identification Number)                             |
| Denmark                          | DK         | `cvr`             | Central Virksomhedsregister (Central Business Register)                                         |
| Dominica                         | DM         | `tin`             | Tax Identification Number                                                                       |
| Dominican Republic               | DO         | `rnc`             | Registro Nacional de Contribuyentes (National Taxpayer Registry)                                |
| Ecuador                          | EC         | `ruc`             | Registro Único de Contribuyentes                                                                |
| Egypt                            | EG         | `tin`             | Tax Identification Number                                                                       |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| El Salvador                      | SV         | `nit`             | Número de Identificación Tributaria                                                             |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Estonia                          | EE         | `rc`              | Register Code                                                                                   |
| Ethiopia                         | ET         | `tin`             | Tax Identification Number                                                                       |
| Fiji                             | FJ         | `tin`             | Tax Identification Number                                                                       |
| Finland                          | FI         | `y_tunnus`        | Business ID                                                                                     |
| France                           | FR         | `siren`           | Système d'Identification du Répertoire des Entreprises (Enterprise Identification System)       |
|                                  |            | `siret`           | Système d'Identification du Répertoire des Établissements (Establishment Identification System) |
| Georgia                          | GE         | `ic`              | Identification Code                                                                             |
| Germany                          | DE         | `ust_idnr`        | Umsatzsteuer-Identifikationsnummer                                                              |
|                                  |            | `crn`             | Company/Commercial Registration Number                                                          |
| Ghana                            | GH         | `tin`             | Tax Identification Number                                                                       |
| Gibraltar                        | GI         | `crn`             | Company/Commercial Registration Number                                                          |
| Greece                           | GR         | `aom`             | Αριθμός Οικονομικής Μονάδας (Economic Unit Number)                                              |
| Guatemala                        | GT         | `nit`             | Número de Identificación Tributaria                                                             |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Haiti                            | HT         | `nif`             | Numéro d'Identification Fiscale                                                                 |
| Honduras                         | HN         | `rtn`             | Registro Tributario Nacional (National Tax Registry)                                            |
| Hong Kong                        | HK         | `brn`             | Business Registration Number                                                                    |
| Hungary                          | HU         | `tin`             | Tax Identification Number                                                                       |
| Iceland                          | IS         | `id`              | Identification Number                                                                           |
| India                            | IN         | `gstin`           | Goods and Services Tax Identification Number                                                    |
|                                  |            | `cin`             | Corporate Identification Number                                                                 |
|                                  |            | `pan`             | Personal Account Number                                                                         |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Indonesia                        | ID         | `npwp`            | Nomor Pokok Wajib Pajak (Taxpayer Identification Number)                                        |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Iraq                             | IQ         | `tin`             | Tax Identification Number                                                                       |
| Ireland                          | IE         | `cro`             | Company Registration Office Number                                                              |
| Isle of Man                      | IM         | `crn`             | Company/Commercial Registration Number                                                          |
| Israel                           | IL         | `crn`             | Company/Commercial Registration Number                                                          |
| Italy                            | IT         | `partita_iva`     | Partita IVA                                                                                     |
|                                  |            | `cf`              | Codice Fiscale                                                                                  |
| Jamaica                          | JM         | `trn`             | Tax Registration Number                                                                         |
| Japan                            | JP         | `hn`              | Hojin Number (Corporate Number)                                                                 |
| Jordan                           | JO         | `tin`             | Tax Identification Number                                                                       |
| Kazakhstan                       | KZ         | `bin`             | Business Identification Number                                                                  |
| Kenya                            | KE         | `pin`             | Personal Identification Number                                                                  |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Korea, Republic of               | KR         | `brn`             | Business Registration Number                                                                    |
| Kuwait                           | KW         | `crn`             | Company/Commercial Registration Number                                                          |
| Kyrgyzstan                       | KG         | `inn`             | Идентификационный Номер Налогоплательщика (Taxpayer Identification Number)                      |
| Lao People's Democratic Republic | LA         | `tin`             | Tax Identification Number                                                                       |
| Latvia                           | LV         | `rn`              | Reģistrācijas Numurs (Registration Number)                                                      |
| Lebanon                          | LB         | `crn`             | Company/Commercial Registration Number                                                          |
| Liberia                          | LR         | `tin`             | Tax Identification Number                                                                       |
| Lithuania                        | LT         | `cc`              | Company Code                                                                                    |
| Luxembourg                       | LU         | `matricule`       | Matricule                                                                                       |
| Macedonia                        | MK         | `tin`             | Tax Identification Number                                                                       |
| Madagascar                       | MG         | `nif`             | Numéro d'Identification Fiscale                                                                 |
| Malaysia                         | MY         | `ssm`             | Suruhanjaya Syarikat Malaysia                                                                   |
|                                  |            | `gstin`           | Goods and Services Tax Identification Number                                                    |
| Malta                            | MT         | `crn`             | Company/Commercial Registration Number                                                          |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Marshall Islands                 | MH         | `en`              | Entity Number                                                                                   |
| Mauritania                       | MR         | `nif`             | Numéro d'Identification Fiscale                                                                 |
| Mauritius                        | MU         | `brn`             | Business Registration Number                                                                    |
| Mexico                           | MX         | `rfc`             | Registro Federal de Contribuyentes (Federal Taxpayer Registry)                                  |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Moldova                          | MD         | `idno`            | Identification Number of Organization                                                           |
| Monaco                           | MC         | `crn`             | Company/Commercial Registration Number                                                          |
| Montenegro                       | ME         | `crn`             | Company/Commercial Registration Number                                                          |
| Morocco                          | MA         | `if`              | Identifiant Fiscal (Tax Identifier)                                                             |
| Mozambique                       | MZ         | `pib`             | Personal Identification of the Business                                                         |
| Namibia                          | NA         | `vat`             | Value Added Tax Number                                                                          |
| Netherlands                      | NL         | `kvk`             | Kamer van Koophandel                                                                            |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| New Zealand                      | NZ         | `gst`             | Goods and Services Tax Number                                                                   |
| Nicaragua                        | NI         | `ruc`             | Registro Único de Contribuyentes                                                                |
| Nigeria                          | NG         | `tin`             | Tax Identification Number                                                                       |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| Norway                           | NO         | `orgnr`           | Organisasjonsnummer                                                                             |
| Oman                             | OM         | `crn`             | Company/Commercial Registration Number                                                          |
| Pakistan                         | PK         | `ntn`             | National Tax Number                                                                             |
| Panama                           | PA         | `ruc`             | Registro Único de Contribuyentes                                                                |
| Peru                             | PE         | `ruc`             | Registro Único de Contribuyentes                                                                |
| Philippines                      | PH         | `tin`             | Tax Identification Number                                                                       |
| Poland                           | PL         | `nip`             | Numer Identyfikacji Podatkowej                                                                  |
|                                  |            | `krs`             | Krajowy Rejestr Sądowy                                                                          |
| Portugal                         | PT         | `nif`             | Número de Identificação Fiscal                                                                  |
| Qatar                            | QA         | `crn`             | Company/Commercial Registration Number                                                          |
| Romania                          | RO         | `cui`             | Cod Unic de Înregistrare                                                                        |
| Russia                           | RU         | `inn`             | Individual Taxpayer Number                                                                      |
|                                  |            | `ogrn`            | Primary State Registration Number                                                               |
| Saint Kitts & Nevis              | KN         | `tin`             | Tax Identification Number                                                                       |
| Saint Lucia                      | LC         | `tin`             | Tax Identification Number                                                                       |
| Saudi Arabia                     | SA         | `crn`             | Company/Commercial Registration Number                                                          |
| Senegal                          | SN         | `ninea`           | Numéro d'Identification Nationale des Entreprises et des Associations                           |
| Singapore                        | SG         | `uen`             | Unique Entity Number                                                                            |
| Slovakia                         | SK         | `ico`             | Identifikačné Číslo Organizácie                                                                 |
| Slovenia                         | SI         | `maticna`         | Matična Številka                                                                                |
| South Africa                     | ZA         | `vat`             | Value Added Tax Number                                                                          |
|                                  |            | `tin`             | Tax Identification Number                                                                       |
| Spain                            | ES         | `nif`             | Numéro d'Identification Fiscale                                                                 |
|                                  |            | `cif`             | Código de Identificación Fiscal (Fiscal Identification Code)                                    |
| Sri Lanka                        | LK         | `brn`             | Business Registration Number                                                                    |
| Sweden                           | SE         | `orgnr`           | Organisationsnummer                                                                             |
| Switzerland                      | CH         | `uid`             | Unternehmens-Identifikationsnummer                                                              |
| Thailand                         | TH         | `tin`             | Tax Identification Number                                                                       |
| Tunisia                          | TN         | `matricule`       | Matricule Fiscale                                                                               |
| Ukraine                          | UA         | `edrpou`          | ЄДРПОУ (Unified State Register of Enterprises and Organizations of Ukraine)                     |
| United Arab Emirates             | AE         | `trn`             | Tax Registration Number                                                                         |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| United Kingdom                   | GB         | `crn`             | Company/Commercial Registration Number                                                          |
|                                  |            | `vat`             | Value Added Tax Number                                                                          |
| United States                    | US         | `ein`             | Employer Identification Number                                                                  |
| Uruguay                          | UY         | `rut`             | Registro Único Tributario                                                                       |
| Uzbekistan                       | UZ         | `inn`             | Individual Taxpayer Number                                                                      |
| Venezuela                        | VE         | `rif`             | Registro de Información Fiscal                                                                  |
| Vietnam                          | VN         | `mst`             | Mã số thuế (Tax Code)                                                                           |
| Yemen                            | YE         | `tin`             | Tax Identification Number                                                                       |
| Zambia                           | ZM         | `tpin`            | Taxpayer Identification Number                                                                  |
| Zimbabwe                         | ZW         | `bp`              | Business Partner Number                                                                         |

### Important Notes

* Some countries support multiple identity/registration types - all accepted types are listed
* Identity type codes are case-sensitive - use them exactly as shown
* For KYB submissions, individuals use the Individual Identity Types table while businesses use the Business Registration Types table
