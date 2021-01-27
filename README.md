# Projekt końcowy

Celem projektu jest utrwalenie wiedzy zdobytej w trakcie szkolenia i wdrożenie jej w życie.
Projekt będzie polegał na stworzeniu sklepu z panelem administracji. W dalszej części opisu będę stosował określenia:
* `Front` dla tej części aplikacji, którą widzi niezalogowany użytkownik
* `Panel Admina` służy do zarządzania Produktami
* `Produkt` - są to przedmioty, który można przeglądać i kupować w naszym sklepie. Wybierz sobie czy mają to być samochody, koszulki, buty, komputery - nie ma większego znaczenia :) Każdy produkt należy do określonej Kategorii, czyli np. Dom, Ogród, Biuro


## Wymagania
* Po wejściu na stronę główną użytkownik widzie 4 sekcje:
  * listę wszystkich Produktów
  * wyszukiwarkę Produktów - po wpisaniu produktu i wciśnięciu entera na liście pojawią się tylko te produkty, które spełniają kryteria
  * po lewej stronie wyświetl kategorie Produktów. Po kliknięciu w kategorię powinniśmy widzieć tylko produkty z danej kategorii
* liczba produktów wyświetlanych na stronie głównej to 10
  * na dole w komponentach typu Tile (kafelek) wyświetl kilka wyróżnionych produktów np. 3
* Po kliknięciu w Produkt użytkownik będę chciał zobaczyć jego dokładniejszy opis oraz cenę
* Produkt można dodać do koszyka, a następnie przejść do widoku koszyka
* Z poziomu koszyka można usunąć produkt oraz zmienić liczbę zamówionych przedmiotów
* Można przejść dalej do płatności lub usunąć wszystkie produkty z koszyka
* Krok z płatnością polega na podaniu przez użytkownika swoich danych oraz przejściu dalej.
* Po przejściu dalej wyświetla się komunikat, że się udało

## Wymagania techniczne
* stwórz nowy projekt za pomocą `create-react-app`
* stwórz plik `.eslintrc.json` z regułami
* Opcjonalnie: możesz wykorzystywać różne narzędzia, takie jak np. prettier. Aby wszystko skonfigurować sprawdzić [to repozytorium]( https://github.com/patrykomiotek/pd-react-app)
* stwórz bibliotekę komponentów, które będą wykorzystywane do zbudowania Frontendu oraz Panelu admina.
* strukturę projektu możesz więc podzielić następująco:

```bash
/src
 |-- components
     |-- Button
     |-- OrderForm
 |-- pages
     |-- admin (strony dostępne tylko dla administratora)
       |-- dashboard
       |-- orders
       |-- products
     |-- products (strony dostępne dla odwiedzających sklep)
     |-- checkout (j. w. )
```

W folderze `components` znajdą się komponenty wykorzystywane przez Front i Panel Admina. Jest to więc biblioteka komponentów wykorzystywanych zarówno na Froncie, jak i Panelu Admina. Do projektu możesz doinstalować [Storybook]( https://storybook.js.org/) i napisać stories.

# Mocking
Wszystkie dane będą zmockowane i będziemy je serwowali za pomocą [Mock Service Workera](https://mswjs.io/). Skorzystaj z [tego przykładu](https://github.com/mswjs/examples/tree/master/examples/rest-react) do wdrożenia.

## API
Do zaprojektowania struktury API polecam narzędzia:
* [Postman](https://www.postman.com/)
* [Insomnia](https://insomnia.rest/)
* [Advanced REST client](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo?hl=pl)

### API Docs
Możemy stworzyć dokumentacje do API za pomocą narzędzia [Swagger](https://swagger.io/) w formacie Open API 3.0

### API client
Do komunikacji wykorzystajmy bibliotekę [axios](https://github.com/axios/axios) w taki sposób, aby istniały dwie instancje - jedna do przeglądania ofert, a druga dostępna z poziomu panelu administratora (będzie wysyłała dodatkowy nagłówek, co opisałem w części z panelem administracyjnym)

## Unit tests
Stwórz testy jednostkowe (unit tests) dla komponentów – korzystając z testing-library

## Integration tests
Stworzy testy integracyjne dla niektórych ścieżek w Cypress – np. przejście użytkownika całego procesu zakupowego.

## Panel administratora
Logowanie do będzie odbywało się za pomocą maila i hasła (w trybie mocków może być to po prostu admin/admin). Przekaż obiekt użytkownika do komponentów Panelu admina (korzystając np. z Contextu)

Informację o tym, czy użytkownik jest zalogowany zachowaj w localStorage. Informacja powinna być zrealizowana w następujący sposób:

Jeśli w localstorage istnieje klucz `accessToken` z ustawioną wartością uuid (możesz ją wygenerować np. tutaj: https://www.uuidgenerator.net/) to znaczy, że użytkownik jest zalogowany.

Taką wartość możesz przechowywać w [zmiennych środowiskowych](https://create-react-app.dev/docs/adding-custom-environment-variables/)

Tę wartość będziemy przekazywali do requestów API od strony administratora.
Wszystkie requesty, jakie będą wychodziły będą posiadały specjalny nagłówek `x-access-token` lub `Authorization`. Do wyboru. Jeśli wykorzystamy `x-access-token` wówczas jako wartość wprowadzimy po prostu `accessToken` np.:

```js
const headers = {
  contentType: 'application/json',
  accept: 'application/json',
  'x-access-token': accessToken
}
```

lub:

```js
const headers = {
  contentType: 'application/json',
  accept: 'application/json',
  authorization: `Bearer ${accessToken}`
}
```

Ten drugi sposób jest zbliżony do [Json Web Token](https://jwt.io/), standardu który jest wykorzystywany w wielu aplikacjach. Rekomenduję zapoznać się z dokumentacją.

* Dla chętnych: później zamiast localstorage możemy wykorzysta cookies do zapisywania tokenu i jego odczytywania.

Z poziomu panelu administratora będziemy mogli zarządza Produktami oraz Kategoriami. Będziemy musieli więc stworzy CRUD (Create, Read, Update, Delete) czyli widoki do przeglądania listy, przeglądania poszczególnego produktu, jego edycji oraz usunięcia. Przyda się też wyszukiwanie na liście.

## Dashboard
Po zalogowaniu się do Panelu Admina powinniśmy zobaczyć statystyki:
* ile było sprzedaży w tym miesiącu
* ile było sprzedaży w tym tygodniu z podziałem na dni (np. pon: 3, wt: 4, śr: 6 itd)
* jakie produkty najczęściej się sprzedają i w jakich ilościach

### Stats
Do wykresów wykorzystaj jedną z bibliotek:
* [React Charts 2](https://github.com/reactchartjs/react-chartjs-2)
* [Chart.js](https://www.chartjs.org/)
* [React Vis](https://uber.github.io/react-vis/)


W panelu administratora możemy skorzystać z jednej z bibliotek:
* [Chakra UI](https://chakra-ui.com/)
* [Material UI](https://material-ui.com/)
* [Ant Design](https://ant.design/)

## Routing
Stwórz komponent `SecureRoute`, który będzie komponentem wyższego rzędu (Higher Order Component - HoC) i będzie sprawdzał, czy użytkownik jest zalogowany czy tez nie.
Jeśli jest, to po prostu wyświetli się przekazany komponent. Jeśli nie jest, to użytkownikowi zostanie wyświetlony komponent z logowaniem.


## Redux
Ca pomocą Reduxa stworz reducer oraz store, który będzie odpowiedzialny za interfejs panelu administratora. Będzie przechowywał notyfikacje, które będą pojawiały się z różnych częściach systemu (czyli gdy będziemy tworzyli ofertę).

## Deployment
Zrób deploy projektu do jednej z usług np.:
* [AWS S3](https://aws.amazon.com/s3/)
* [Firebase Hosting](https://firebase.google.com/docs/hosting)
* [GitHub Pages](https://pages.github.com/)
* [Heroku](https://www.heroku.com/)
* [Netlify](https://www.netlify.com/)
* [Vercel](https://vercel.com/)

Zaktualizuj plik README.md dodając link. Chcemy, aby osoba odwiedzająca Twoje repozytorium mogła zobaczy działającą aplikację.

Powodzenia! :) 💪
