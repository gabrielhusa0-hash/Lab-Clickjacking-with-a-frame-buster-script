# PortSwigger Lab: Clickjacking with a frame buster script

# 📚 Studijní materiál
Tento repozitář slouží jako můj osobní studijní zápisník a přehled řešení laboratoří z platformy PortSwigger Web Security Academy.

# 📌 Popis zranitelnosti a principu

Jde o zranitelnost typu Clickjacking (UI Redressing), kde se cílová stránka snažila bránit proti načtení do iframe rámečku pomocí obranného JavaScriptu (frame buster script).

Tento skript má za úkol detekovat, zda se stránka nenachází v iframe, a pokud ano, pokusí se z něj "vyskočit" tím, že přesměruje celé okno prohlížeče na svou vlastní adresu. Tím pádem by útočníkovi překazil možnost zprůhlednit cílový web a překrýt ho vlastním tlačítkem.

Náš úkol byl tento frame buster obojetně vyřadit z provozu a přesto přimět uživatele nevědomky odeslat formulář na změnu e-mailu.

🛠 Postup řešení

Na stránce laboratoře jsem klikl na tlačítko Go to exploit server.

Do pole Body jsem vložil níže uvedený exploit kód:

<style>
    iframe {
        position: relative;
        width: 700px;
        height: 600px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position: absolute;
        top: 450px;
        left: 50px;
    }
</style>
</head>
<body>

<div>Click me</div>
<iframe sandbox="allow-forms" src="https://0a1400ee037c21a083f20ac000460060.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>


Klikl jsem na Store pro uložení exploitu.

Následně jsem klikl na Deliver exploit to victim k odeslání oběti.

⚠️ Poznámka: V tagu iframe src="..." musíte vždy nahradit URL adresu svojí vlastní aktuální adresou laboratoře!

# 💡 Technické vysvětlení  a proč to fungovalo?

Představ si, že webová stránka oběti má v sobě "hlídacího psa" v podobě JavaScriptu (frame buster). Jakmile zjistí, že ho někdo zavřel do těsného rámečku (iframe) na cizím webu, hned spustí poplach a přinutí prohlížeč otevřít stránku naplno.

My jsme na ten rámeček poučili trik v pod podobě atributu sandbox="allow-forms":

Umlčení hlídacího psa (vyřazení JavaScriptu): Samotné nastavení sandbox zakáže uvnitř iframe úplně všechny skripty. Hlídací JavaScript se tak vůbec nespustí, nezjistí, že je v rámečku, a nemůže stránku přesměrovat.

Povolení odeslání formuláře: Kdybychom dali jen čistý sandbox, nefungovalo by ani tlačítko. Přidáním allow-forms jsme ale prohlížeči řekli: "JavaScript uvnitř zakazuji, ale odesílání formulářů povoluji."

Překrytí vrstev: Stejně jako u klasického clickjackingu máme podložený nápis Click me pod téměř neviditelným iframe (opacity: 0.1). Uživatel klikne na nápis, ale kliknutí propadne do neviditelného tlačítka pro změnu e-mailu.

Díky této kombinaci zůstal cílový web v iframe zablokovaný před přesměrováním, ale formulář na změnu e-mailu po kliknutí oběti normálně prošel.