# Git-julekalender
Dette er en adventskalender med git-fakta jeg lagde til julen 2020. Merk at dette var et læringsprosjekt, så det kan finnes feil eller unøyaktigheter, selv om jeg forsøkt å unngå det. Hvis du finner noe kan du lage en issue eller pull request :)
## 01.  
For å legge til flere avsnitt i commit-meldinger, bruk flere '-m'-argumenter, slik: `git commit -m "Tittel" -m "Body"`.

## 02.  
For å søke gjennom commit-meldinger, bruk 
`git log --grep="søketekst"`.

## 03.
For å se hva som vil commites når du har add-et, bruk `git diff --staged`. Det viser altså _ikke_ endringer som ikke er add-et.

## 04.  
Når du ikke vet i hvilken commit en bug ble introdusert, bruk `git bisect.` Den binærsøker commitene ved å checkoute en serie av dem, så kjører du koden og sjekker om buggen var i den commiten. Du kan spesifisere filer eller mapper for å filtrere commitsene som skal sjekkes på med vanlig `--`-notasjon. Alternativt kan du kjøre hele greia automatisk ved å skrive et script som tester koden og endrer hvilken exit-kode den returnerer avhengig av om buggen er til stede. Se [her](https://git-scm.com/docs/git-bisect) for detaljer. 

For å starte manuell bisect, skriv `git bisect start`. Checkout den tidligste commiten du vet har buggen. Skriv `git bisect bad`. Checkout den nyeste commiten du vet ikke har buggen, og skriv `git bisect good`. Git vil da checke ut en commit, du sjekker om buggen eksisterer, og skriver `git bisect good` eller `git bisect bad`. Git går da videre, og gjentar til du har funnet commiten som introduserte buggen. 

Såklart trenger ikke dette handle om å finne en bug, og da kan det være tydeligere å skrive `new` og `old` i stedet for `bad` og `good`.

## 05.  
`git show <ref>` er en rask måte å se meldingen og endringene i en commit på - den gir diffen fra commiten til den forrige. Alternativt kan du se tilstanden til en fil i den commiten ved `git show <ref>/<filpath>`.

## 06.  
Man kan adde kun deler av en fil med `git add -p [filer]`, du får en interaktiv guide. Alternativt kan man bruke `git add -i` for å gjøre hele add-prosessen interaktiv. For total kontroll over hva som addes kan du bruke "e"-modus i patch-uiet. Da får du opp tekst-editor med en liten guide.

## 07.  
For å fast-forwarde en branch uten å checkoute, skriv f.eks. `git fetch origin master:master`. Hvis du (som meg) blir promptet for ssh-passord hver gang du puller kan du bruke `git fetch . origin/master:master`. Forskjellen er at du spesifiserer det lokale repoet (`.`) som remote, sånn at ingenting må hentes.

## 08.  
For å stille en fil tilbake til tilstanden den hadde ved en viss commit, skriv `git checkout [branch] -- <filer>`. Hvis branch ikke spesifiseres tolkes det som det nåværende. Dette kan brukes for å un-stage endringer.

## 09.  
For å pushe en tag til remote, bruk `git push --follow-tags` fra en commit som er en descendent av commiten taggen er på (dvs. det må gå en linje av commits bakover fra den du er på til den med taggen). Kun annotated tags vil pushes. De lages med `git tag -a <navn>` (annotated) eller `git tag -s <navn>` (signed). I stedet for å spesifisere follow tags hver gang kan man sette `git config --global push.followTags true`. Alternativt kan man pushe én tag manuelt med `git push <remote> <tag>`.

## 10.  
For å få samme statistikk-oversikt i loggen som når du puller, bruk `git log --stat`. For å få et fint graf-view, bruk `git log --all --decorate --oneline --graph` (aka. "A DOG" :dog2:). For å se GPG-signaturer, bruk `git log --show-signature`. For å skrive ditt eget log-format, bruk `git log --format=<format>`, hvor `<format>` er f.eks. `"%Cred%h %Cblue%an: %Creset%s"`, som betyr: rød farge; kort hash; mellomrom; blå farge; forfatters navn; kolon mellomrom; standard farge; commit-melding. For flere koder (og presets), se `git log --help`.

## 11.  
For å forke et repo uten å bruke knappene til GitHub og GitLab, lag et tomt repo, clone repoet du vil forke. `git remote set-url origin <url>`, push. Hvis du vil beholde det du forket fra som upstream, kjør i stedet `git remote rename origin upstream`, `git remote add origin <url>`, push (sjekk at du pusher til riktig remote). Hvis GitHub eller GitLab lagde et commit du vil overskrive må du force pushe.

## 12.  
Dagen har kommet for interactive rebase. For detaljert gjennomgang av det viktige, se [her](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History). Interactive rebase brukes vanligvis for å skrive om historien. Jeg anbefaler å lage en ny branch før man begynner å rebase. Begynn med `git rebase -i <commit>`, hvor <commit> er *parent-en* til den siste du vil redigere. Du kan referere til den commiten med f.eks. `HEAD~<N>`, hvor <N> er antall commits du vil redigere. Det betyr bare commit nr N bakover fra der du er (med `HEAD~0` som nåværende). Du får opp tekst-editor med commitene, men merk at de står i *omvendt* rekkefølge, dvs. nyeste på bunnen. Du kan se for deg hver linje som en kommando git kjører, fra øverst til nederst. For å gjøre en handling på en commit, endre "pick" før commit-hashen og meldingen til et annet alternativ. 

Dette er alternativene du får for hver commit:
* pick: behold commit, ikke gjør endringer
* reword: kun endre commit-melding
* edit: sjekk ut commiten, rediger filer, amend, continue. Hvis du vil dele commiten i flere commits kan du kjøre `git reset HEAD^` for å gå én commit tilbake men beholde endringene fra commiten som ikke-staged endringer. Du kan så adde de delene du vil ha i første commit, commite (uten amend), osv., og continue til slutt.
* squash: kombinér denne commiten med den forrige. Hvis flere påfølgende har squash, kombinér alle dem med den første høyere oppe som ikke har det. Du blir promptet for å skrive en ny commit-melding for dem kombinerte commiten.
* fixup: som over, men du trenger ikke promptes for ny melding siden den bruker meldingen til den første høyere oppe som ikke er fixup
* drop: samme som å fjerne linja med commiten. Vil fjerne hele commiten, inkludert endringer.
Følgende er ikke alternativer for et commit, men kommandoer man skriver alene på en linje mellom to commits/kommandoer:
* exec: kjør en kommando, stopp hvis kommandoen "feiler" (returnerer en kode som ikke er 0). Eksempelvis kan man sjekke at testene passer på en commit ved å skrive `exec mvn test` på en ny linje under linja til commiten du vil teste. For å sjekke ut og teste alle kan du bruke ikke-interactive `git rebase <parent til eldste commit> --exec "<kommando>"`.
* break: pause her. Lar deg se hvordan det ser ut etter forrige commit
* label, reset, merge: ignorer disse, de brukes internt av git til merging. Hvis du *må* vite, se her.

## 13.  
Dagens tema er `git reset`. Det finnes tre viktige moduser: 
* `--mixed` (hverken soft eller hard): Dette er standard hvis ikke annet er oppgitt. Den vil resette indexen (aka staging area, endringene som er addet) til oppgitte filer til tilstanden de hadde ved gitt commit, pluss at den vil flytte current branch til å peke på gitt commit. Eksempelvis vil `git reset README.md license.txt` un-adde endringene i license.txt og README.md, fordi den resetter filene til siste commitede tilstand og branchen allerede peker på HEAD. Vi måtte ikke spesifisere at vi ville resette dem likt HEAD (current commit) fordi det er default, men det hadde vært så lett som å føye det til etter alle filene. Alternativt kan man bruke `git reset <commit> -- <filer>` for å være sikker på at git ikke prøver å tolke filer som commits eller omvendt. Husk også at en branch bare er en referanse til en commit, så det vil også aksepteres i stedet for en spesifikk commit. Hvis vi vil fjerne alle filene fra indexen trenger vi kun `git reset`. Når mål-commiten er HEAD er dette den inverse formen av `git add`, og man kan til og med bruke `-p` for å velge delerav filene å resette, som i `git add -p`. Hvis man velger en annen commit enn HEAD kan man bygge videre på en commit fra en annen branch, men merk at working directory ikke endres, og at den nåværende branchen flyttes til samme commit. Hvis det ønskes å resette working directory lik index kan man lese på hvordan det gjøres med `git restore`.
* `--soft`: endrer kun hvilken commit current branch peker på, ikke indexen.
* `--hard`: flytter branchen til å peke på den oppgitte commiten, tømmer index og setter working directory likt tilstanden i commiten.

## 14.  
Dagens tema er hvordan git lagrer filer. Det kritiske å vite her er at git konseptuelt[1] _ikke_ lagrer en delta (aka diff, aka liste over endringer) fra én commit til neste. I stedet lagrer git et snapshot av alle filene i hele repositoryet ved hver commit. For at dette ikke skal kreve ekstremt mye plass, vil git bare lagre en fil med et konstant innhold én gang. Det vil si at når du kun endrer én fil, er det kun én fil som må lagres. Dette gjøres ved at hver fils innhold hashes med SHA-1, og innholdet lagres i et hashmap med hashen som nøkkel. 

Når man skjønner at git konseptuelt ikke lagrer deltaer, men hele filer, er det enklere å skjønne hvordan git-kommandoer fungerer. For å gjøre et cherry-pick (velge kun én commit av en serie og legge den til en annen branch) må git først regne ut diffen fra den commiten til forrige, og prøve å patche target-branchen med den diffen. 

[1] Trivia: Når det kommer til å faktisk lagre bitene kan git faktisk sette sammen flere versjoner av samme fil i ett komprimert lager som bruker deltaer i stedet for hele filen, men det gjør ingen praktisk forskjell for grensesnittet til git. For binærfiler kan ikke git finne deltaer, så den må alltid lagre hele filen på nytt hver gang noe endres, hvilket er grunnen til at binærfiler ikke er anbefalt å putte i git.

## 15.  
`git restore` er en ny (halv-eksperimentell) kommando (mulig man må oppdatere git for å få den) som skal gjøre det mer brukervennlig å håndtere versjonen til filer.
Den forsøker å erstatte  
* `git checkout <commit> -- <filer>` for å resette filer til en annen versjon, med `git restore [--source <commit>] <filer>`. Hvis <commit> ikke oppgis er index (staging area) default.  
* `git reset <filer>` for å un-stage endringer, med `git restore --staged <filer>`.  
* Begge, med `git restore [--source <commit>] --staged --workspace <filer>`.  
`--workspace` kan erstattes med `-W`. `--staged` kan erstattes med `-S`. `--source` kan erstattes med `-s`.
En ting som er kan være verdt å merke seg er at når jeg skriver <commit> ber git ofte egentlig om en "tree-ish", som betyr at du kan resette en fil til innholdet til en helt annen fil i en vilkårlig commit - eller til og med utenfor et commit. Se [her](https://stackoverflow.com/a/13754113) hvis du er nysgjerrig.

## 16.  
Git har hele tre nivåer med config, hvor det mest spesifikke nivået alltid tar presedens. De er:
1. `git config --system <kommando>`. Lagres i git-installasjonen
2. `git config --global <kommando>`. Lagres i brukermappen.
3. `git config [--local] <kommando>`. Lagres i det lokale repoet (.git-mappen i din clone av prosjektet).
Et eksempel på en kommando kan være 
* `--list`, for å liste opp alle innstillingene i den fila. Bruk `--show-origin` når du ikke spesifiserer fil for å se hvilken fil innstillingen er satt i. Brul `--show-scope` på samme måte hvis du bare vil se "global", "local", etc. isf. path. Merk at med `--list` får du alle filene hvis du ikke spesifiserer, i stedet for bare local. 
* `<innstilling> <verdi>` for å sette en innstilling
* `--get <innstilling>` for å se den aktive verdien, dvs. den som faktisk tar presedens.
* `--get-all <innstilling>` for å se alle linjene som definerer en gitt innstilling.
* `--unset <instilling>` for å fjerne en innstilling (sette tilbake til default).
* `--unset-all <innstilling>` for å fjerne alle linjer som definerer key-en. Merk at dette ikke har virkningsområde over alle filene, men bare linjer med samme key i én fil.
* `--replace-all <innstilling> <verdi>` for det samme som over pluss å sette ny verdi for den keyen i den gitte filen.
Grunnen til at `--<kommando>-all` eksisterer er fordi git tillater flere linjer for samme key, men den bruker alltid den siste.

## 17.  
Git clean er laget for å fjerne untrackede (og potensielt ignorerte) filer fra filsystemet, fra nåværende mappe og ned. Den ikke slette untracked mapper uten `-d`. Hvis du vil vite hva den kommer til å slette før du kjører kommandoen kan du bruke `--dry-run`, aka `-n`. Hvis du vil at den også skal slette ignorerte filer, bruk `-x`. Hvis du bare vil slette ignorerte, bruk `-X`. Hvis du vil ekskludere noen filer, bruk `--exclude=<pattern>` (aka `-e <pattern>`). Hvis du kun vil cleane noen mapper kan du skrive dem etter hverandre til slutt. Når du har bygd ferdig kommandoen må du bruke `-f` for å faktisk slette dem, eller sette config `clean.requireForce false`.
Eksempel: `git clean -df core/`.

## 18.  
Som mange Unix-baserte verktøy bruker Git [glob patterns](https://en.m.wikipedia.org/wiki/Glob_(programming)) når du må spesifisere filer/mapper. De er basically en veldig enkel versjon av regex, med fire wildcards[1]: 
* `*` for "hva som helst her uavhengig av lengde, bortsett fra `/`". Dvs. at kun filer i den nåværende mappen kan inkluderes med denne, siden filer i andre mapper vil ha skråstrek i path-en.
* `?` for "én karakter her, bortsett fra `/`". 
* `**` for "absolutt hva som helst her, også `/`". Brukes for rekursive søk.
* `[aBz]` for å matche én av karakterene inni, som i regex. Ranges (`[a-z]`) funker også.
Merk at ingen av disse spesifiserer antallet av det som kom rett før som i regex, de er frittstående. Dvs. `*.java` som glob er ekvivalent med `.*\.java` som regex. Man kan ikke representere regexen `[ab]+` i glob[2]. Merk også at glob patterns alltid må matche hele strengen, så `karl.java.banan` blir ikke matcha av `*.java`. 

`.gitignore` er ett av bruksområdene. Den fungerer ved å gå inn i hver mappe rekursivt, sjekke om filnavnene i den matcher noen av patternene (unntatt patterns med en `/` et annet sted enn helt til slutt), og i så fall markere filen som ignorert. Eksempelvis kan man bruke `*.iml` for å ignorere IntelliJ-data-filer. Hvis et pattern slutter med `/` matcher den bare mapper (uten vil den matche både mapper og filer). Hvis den har en un-escaped `/` et annet sted matcher det patternet bare relativt til `.gitignore`-filen. E.g. vil `idea/` ignorere alle mapper med det navnet, mens `/idea/` bare ignorerer `idea`-mapper i samme mappe som `.gitignore`, og `/core/idea`/ eller `core/idea/` bare matcher dem i undermappen `core` av mappen `.gitignore` er i. Flere `.gitignore-filer` kan brukes, den lavest i mappetreet tar presedens. Til slutt vil `!` før et pattern negate patternet, sånn at de som matcher vil inkluderes selv om de blir ignorert av et annet pattern. Det går ikke an å override at disse inkluderes uten å ha en annen .`gitignore` med høyere presedens som ignorerer dem. 

[1]: Wildcardene matcher vanligvis ikke en leading `.`, da disse er ment å indikere skjulte filer i Unix-baserte systemer. Man må derfor spesifisere den eksplisitt, med f.eks. `.*` eller `!.git*`. Git ser ut til å følge denne konvensjonen til vanlig (e.g. `git add *` vil matche `.gitignore`), men ikke i `.gitignore`-patterns (`*` vil ignorere `.kanin` (og interessant nok `.gitignore`)).

[2]: Det er derimot mulig i extended glob, men det støtter ikke git.

[Source](https://git-scm.com/docs/gitignore).

## 19.  
`git reflog [ref]` viser endringsloggen til en referanse (HEAD, branches). Default er HEAD. Vi kan bruke kommandoen for å finne ut hvor vi har vært nylig (git sletter for gammel historie automatisk). Reflogen til HEAD viser hvilken branch den pekte på, og hvilken commit branchen pekte på, på hvert tidspunkt. Man kan f.eks. bruke dette for å finne igjen en gammel commit fra før en rebase. Da kan det være lurt å sjekke reflogen til branchen i stedet for å filtrere litt mer. Man kan bruke shorthand `<ref>@{<N>}` for å referere til hvor referansen var for N moves siden. Merk at man på noen plattformer (windows) må putte hermetegn rundt alle uttrykk med `@`.

## 20.  
Bruk `git log --follow -- <fil>` for å se commits som påvirker en fil, selv hvis den endret navn. Merk at git ikke lagrer renames noe sted, så de må finnes probabilistisk hver gang basert på hvor mange linjer som er like. Merk at git bare gir opp hvis antall slettede filer pluss antall nye filer i en commit er større enn en konstant. Endre `diff.renamelimit` i configen hvis du trenger mer aggressiv sjekk. For å bare gjøre det denne gangen, bruk `git log --follow -c diff.renamelimit=<tall> -- <fil>`.

## 21.  
Dagens luke er en måte å merge to distinkte repos på. Jeg brukte denne da jeg startet et eget OOP-repo, og så senere ville hente inn øvingene automatisk fra fagstab-repoet, hvis jeg husker riktig. Da merget jeg fagstabens inn i mitt, og beholdt fagstaben som upstream.
1. Gå til repoet du vil merge det andre inn i.
2. git remote add <url/til/andre/repo> <remote-name>
3. git fetch <remote-name>
4. git branch --track <merge-branch-name> <remote-name>/<target-branch>
5. git merge <merge-branch-name> --allow-unrelated-histories
6. Hvis merge conflicts: resolve og commit.
7. Du kan nå slette <merge-branch-name> (og <remote-name>, hvis du vil).


## 22.  
Noen gang glemt hvor og når i alle dager noe kode eksisterte? Git got your back. For å finne alle filer som har tekst akkurat nå som matcher søket (trenger ikke matche en hel linje), bruk `git grep -F '<søk>`. For regex, bruk `git grep -e '<regex>'`. For flere alternativer (det er altfor mange til å skrive her), se dokumentasjonendokumentasjonendokumentasjonendokumentasjonen.

Hvis du ikke vet hvilke(n) commit(s) teksten eksisterer i, bruk `git rev-list --all | xargs git grep -e '<regex>'`. Rev-list gir listen over alle commits som kan nås fra branches (dvs. at den ikke finner commits fra f.eks. slettede branches). Xargs mater outputet inn i git grep-kommandoen med magi så det blir riktig argument. Git grep søker gjennom hvert av commitene den får fra xargs for å lete etter regexen. Merk at dette ikke gir bare commiten som la til teksten; dette har å gjøre med at commits ikke er differ. Git grep søker gjennom alle filene i repository-snapshotet som hører til commiten, ikke diffen. 

Hvis du bare vil ha commiten som la til teksten, kan du sette rev-list til å begynne ved starten, og så endre litt på kommandoen så den stopper ved første resultat (f.eks. med `... | head -1`). En enklere og bedre måte vil være å bruke dette SO-svaret.

## 23.  
Når du har kodet en stund og vil bryte opp det du har gjort i flere commits, kan du begynne med `git add -i`. Når du har lagt til alt du vil commite er det veldig lurt å sjekke om programmet kjører i den commiten - et mål for git er at hvert eneste commit kan kjøres. For å finne ut om det holder er dette min favorittmåte:
1. Add med `git add -i`
2. `git stash --keep-index --include-untracked` (aka `git stash -ku`)
3. Kjør koden og sjekk at alt funker/kjør testene
4. Eventuelt fiks feil og add
5. Commit
6. `git stash pop` hvis du er modig, `git stash apply` etterfulgt av `git stash drop` hvis det gikk bra for oss vanlige dødelige. 
7. Fiks eventuelle conflicts (kan skje hvis du måtte fikse feil)
8. Repeat
I forget for å bruke interactive adding kan man bruke `git stash -p`, men jeg synes det er mindre oversiktlig. Fordelen da er at man ikke trenger å stashe det som skal med i denne commiten, hvilket reduserer muligheten for konflikter hvis du må endre noe.

Ekstratip: `git checkout -- <filer>` resetter til staged endringer.

## 24.  
Gratulerer med vel gjennomført kurs, nå har vi kommet til slutte. Den siste luka blir et krimmysterie som kan løses med git: [Gitstery](https://github.com/nivbend/gitstery) 🎅