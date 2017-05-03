## Rozdělení aplikace na moduly

Každý projekt lze rozdělit do několika logických částí s jasně definovanou funkcí. Vezměme si například Instagram. Hlavní část aplikace je feed zpráv, kde můžeme prohlížet fotografie, dávat lajky, přejít na obrazovku pro vložení komentářě nebo odeslat zprávu. Další záložka je seznam lajků, odkud můžeme přejít na konkrétní fotografii. Každá z těchto obrazovek je samostatný prvek, sloužící k plnění jasného úkolu, a z které můžeme iniciovat přechod na jiné obrazovky, pokud je to požadováno. Tyto prvky nazýváme aplikační moduly.
V jednoduchých případech jedna obrazovka odpovídá jednomu modulu, ale pokud jsou obrazovky složité, měli bychom je rozdělit na více modulů. Pro příklad opět z aplikace Instagram - obrazovka profilu. Obrazovku je možné rozdělit modul fotky (1), informace o uživatelském profilu (2), lišta pro přepnutí seznamu fotografií na jiná zobrazení (3).

![Profil Instagram](../Resources/instagram_example_serkrapiv.png)

## Struktura modulu VIPER

Pokud má modul plnit svůj účel, je nutné vyřešit několik úkolů. Je vyžadována nějaká aplikační logika, propojení se sítí nebo databází, vykreslování uživatelského rozhranní. Tyto všechny úkoly jsou rozděleny do jednolivých prvků Viperu a my si popíšeme úlohu každého z nich a také způsob, jak spolu vzájemně komunikují. 
Takže prvky VIPER jsou následující:

**View:** je zodpovědný za zobrazování dat na obrazovce - vykreslování UI a také za přenos uživatelkých interakcí do Presenteru. Je to pasivní prvek, nikdy sám nežádá o data, vždy je dostává přes Presenter.

**Interactor:** pokrývá veškerou aplikační logiku potřebnou pro chod aplikace

**Presenter:** přijímá od **View** informace o interakcích uživatele a přeposílá je jako požadavky na  **Router**, **Interactor**, a také přijímá data od **Interactor**, které napopak vrací do **View** pro zobrazení uživateli.

**Entity**: datové objekty bez jakékoliv aplikační logiky.

**Router**: je zodpovědný za navigaci mezi jednotlivými moduly v aplikaci.

## Co jsme změnili

Takže VIPER původně představila společnost [Mutual Mobile](https://www.objc.io/issues/13-architecture/viper/). my jsme začali tento postup aktivně používat a záhy jsme si uvědomili, že má několik problematických míst:

1) V původní verzi VIPERu byl za routování mezi moduly zodpovědný prvek zvaný Wireframe. Zároveň byl ale zodpovědný za komunikaci mezi jednotlivými prvky v modulu. To je ale špatně, protože to porušuje zásadu [Single Responsibility](https://en.wikipedia.org/wiki/Single_responsibility_principle). 

Rozhodli jsem se rozdělit prvek Wireframe na dva. První část, Router, je zodpovědný pouze za komunikaci mezi jednotlivými moduly v aplikace. Druhý část, Assembly, je zodpovědná za sestavení modulu z jednotlivých prvků a udržování vztahů mezi jednotlivými prvky v modulu. V našich aplikacích se pro tento účel používá [Typhoon](https://github.com/appsquickly/Typhoon), bezvadná knihovna pro Dependency Injection, která nám dovoluje se zbavit závislostí jednotlivých prvků v modulu na sobě.

2) Interaktor implementuje veškerou aplikační logiku. Vypadá to sice docela komplikovaně, protože to může představovat hodně úkolů, ale ty by měli být rozděleny mezi různé třídy. Krom toho se takový úkol může opakovat i v Interaktoru jiných modulů. Potřebovali jsme tohle dobře promyslet, abychom kód nemuseli znovu psát pro každý modul.

Rozhodli jsme se přidat další vrstvu služeb. Service - objekt zodpovědný za práci se svým specifickým typem datového modelu. Například služba News je zodpovědná za získání seznamu novinek pro určitou kategorii, stejně tak i detail jednotlivých zpráv. Autorizační služba je zodpovědná za autentofikaci uživatele, změnu hesla a tak dále. Čili služby mají závislosti na nižších objektech, která odpovídají za obsluhu sítě nebo datového úložiště.

Služby se injektují do interaktoru. Data jsou zde zpracována, je aplikována veškerá logika a dále jsou přenášeny do Presenteru. My jsme se v týmu dohodli, že v případě použití Core Data se NSManagedObject nezpracováná mimo Interaktor. Proto také v Interaktoru dochází k přeměně `NSManagedObject` na obyčejný `NSObject`.

3) Ve VIPERu se často jako View používá `UIViewController`. Ale někdy se v kontroléru vystytuje kód, který přímo nesouvisí s View. Například práce s kolekcemi zobrazované tabulkami. Né nadarmo jsou pro páci s těmito objekty vytvořeny protokoly. Předpokládá se, že operace s těmito objekty by mělo být realizováno v samosttaných objektech. Ale v příkladě od Mutual Mobile pro práci s tabulkami přímo včleněn do `UIViewController`.

Нам это не понравилось, и мы решили, что View в общем случае является не одним объектом, а слоем. Помимо контроллера этот слой может содержать дополнительные объекты, которые инжектируются в контроллер и берут на себя часть его работы. Примером такого объекта в наших проектах является DataDisplayManager, реализующий методы `UITableViewDataSource` и `UITableViewDelegate`, или их аналоги для коллекций.

4) Вопрос передачи данных между модулями в оригинальном VIPER не охвачен. Наше решение этой проблемы описано в главе “Переходы между модулями”, подробно на нем останавливаться не будем. Скажем лишь, что у каждого модуля могут быть интерфейсы входа и выхода - протоколы ModuleInput и ModuleOutput. Первый отвечает за передачу входных данных, например идентификатора статьи для экрана, отвечающего за отображение статьи. Второй отвечает за передачу результата работы модуля заинтересованному объекту. Например, при работе с модулем настроек мы можем передать наружу пункт меню, выбранный пользователем. 

## Итоговая схема модуля

Для иллюстрации всего описанного выше предлагаем ознакомиться с итоговой схемой модуля VIPER. Пугаться не стоит, далеко не каждый модуль должен содержать такое количество объектов. Целью этой схемы было максимально полно отобразить наш подход к архитектуре на примере сложного модуля.

![Схема модуля VIPER](../Resources/module_structure.png)
