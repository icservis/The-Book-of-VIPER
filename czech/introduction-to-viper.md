**VIPER** - to je návrhový vzor mobilních aplikací (zejména iOS), založený na myšlenkách [Roberta Martina](http://blog.cleancoder.com/), představených v článku [The Clean Architecture](https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html).

![Clean Architecture](../Resources/clean-architecture.png)

#### Hlavní úkoly, které pomáhá řešit VIPER

- Kompletní pokrytí testy  vrstvy Presentation, které zpravidla zajišťují *Massive View Controllers*.
- Rozdělení velkých tříd aplikace na menší sady s jasně rozdělenenými kompetencemi.

Je důležité si uvědomit, že VIPER není v žádném případě soubor přísných pravidel a šablon. Spíše se jedná o seznam doporučení, který nám pomáhají vybudovat flexibilní architekturu mobilní aplikace. My, iOS tým Rambler&Co, jsme přizpůsobili některé z principů kanonizace a na jejich základě jsme vytvořili soubor osvědčených postupů při implementaci zadání aplikace.

Zpočátku může architektura VIPERu připadat těžkopádná a nepochopitelná a to zejména tehdy, pokud není vývojář zvyklý pracovat v týmu na velkých projektech a to zejména kvůli nepochopení potřeby nezávislosti aplikačních modulů na sobě a nutnosti maximálního pokrytí kódu jednotkovými testy. Nicméně celá sada přístupů má své přednosti i pro menší aplikace.

#### Hlavní výhody a nevýhody návrhového vzoru VIPER

Klady:

- Zkvalitnění vrstvy Presentation pomocí jednotkový testů.
- Naprostá nezávislost modulů na sobě - to umožňuje nezávislý rozvoj a opětovné využití ať ve vlastní aplikaci nebo i v jiných aplikacích.
- Výrazně vyšší zaměnitelnost vývojářů vzhledem k mnohem jednodušší implementaci a údržbě díky univerzálním postupům platným pro všechny moduly.

Zápory:

- Prudký nárůst počtu tříd v projektu, vysoká složitost vytvoření nového modulu.
- Některé zásady neodpovídají úplně doporučeným postupům při implementaci UIKitu od Apple.
- Nedostatek veřejně dostupných ukázek implementace, best practices nebo příkladů použití v kompletních aplikacích.

V další části podrobně rozebereme každý bod z tohoto seznamu, včetně diskuse o tom, jak tyto problémy eliminovat.

#### Historie VIPERu v kostce

- **08.2012** - článek [The Clean Architecture](https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html) od Rоberta Martina.
- **12.2013** - článek [Introduction to VIPER](http://mutualmobile.github.io/blog/2013/12/04/viper-introduction/) od společnosti [MutualMobile](http://mutualmobile.github.io/).
- **06.2014** - vydání objc.io #13 s článkem [Architecting iOS Apps with VIPER](https://www.objc.io/issues/13-architecture/viper/) také od MutualMobile.
- **07.2014** - [vydání podcast iPhreaks Show] (https://itunes.apple.com/ru/podcast/the-iphreaks-show/id634022060?mt=2&i=316803444), kde MutualMobile mluví o tom, jak se VIPER objevil, které otázky byly rozhodující a jak jej využívat v aplikacích.
- **04.2015** - V rámci místního Hackatonu v Rambler&Co byla napsána první aplikace pomocí návrhového vzoru VIPER.
- **12.2015** - v Rambler&Co obdželi více jak 10 objednávek na použití VIPERu, a to jak v současné době rozpracovaných tak již  [publikovaných aplikací na AppStore](https://itunes.apple.com/ru/developer/rambler-internet-holdings/id395455934).