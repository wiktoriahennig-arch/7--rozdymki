<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Baza pytań: Neurologia</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: #f4f7f6;
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }
        .quiz-container {
            background-color: #fff;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 700px;
        }
        h2 { margin-top: 0; font-size: 1.3rem; line-height: 1.5; }
        .option {
            display: block;
            margin: 10px 0;
            padding: 12px;
            background-color: #f8f9fa;
            border-radius: 8px;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid #e9ecef;
        }
        .option:hover { background-color: #e2e6ea; }
        .option.correct { background-color: #d4edda; border-color: #c3e6cb; }
        .option.wrong { background-color: #f8d7da; border-color: #f5c6cb; }
        .option input { margin-right: 12px; transform: scale(1.2); }
        button {
            margin-top: 20px;
            padding: 12px 24px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1.1rem;
            width: 100%;
        }
        button:hover { background-color: #0056b3; }
        #progress { margin-bottom: 15px; font-weight: bold; color: #6c757d; }
        #result { margin-top: 20px; font-weight: bold; font-size: 1.3rem; text-align: center; }
    </style>
</head>
<body>

<div class="quiz-container">
    <div id="quiz">
        <div id="progress">Pytanie 1 z 59</div>
        <h2 id="question">Ładowanie pytania...</h2>
        <div id="options"></div>
        <button id="submit">Sprawdź odpowiedź</button>
    </div>
    <div id="result"></div>
</div>

<script>
    const quizData = [
        { q: "1. Podawanie preparatu Gilenya u chorych na SM może powodować:", o: ["A. Tachykardię", "B. Wzrost RR", "C. Bradykardię", "D. Spadek RR"], c: 2 },
        { q: "2. Natalizumab zaliczamy do przeciwciał monoklonalnych i podajemy go:", o: ["A. Doustnie 1x dziennie przed posiłkiem", "B. Dożylnie co 4 tygodnie we wlewie dożylnym przez 1 godzinę", "C. Dożylnie co 3 miesiące w pompie infuzyjnej przez 4 godziny", "D. 120 mg dwa razy na dobę..."], c: 1 },
        { q: "3. O jakim powikłaniu poinformujesz pacjenta przed podaniem Mitoxantronu:", o: ["A. Niebiesko-zielone zabarwienie moczu", "B. Objawy grypopodobne", "C. Ból i obrzęk w miejscu wkłucia", "D. Przejściowe nasilenie objawów SM"], c: 0 },
        { q: "4. Co oznacza pojęcia spastyczność:", o: ["A. Fizjologiczne napięcie mięśniowe", "B. Obniżone napięcie mięśniowe", "C. Wzmożone napięcie mięśniowe inaczej sztywność", "D. Napięcie pomocnicze"], c: 2 },
        { q: "5. Grupa objawów neuropsychologicznych (ignorowanie jednej strony ciała/przestrzeni) to:", o: ["A. Stan nie mający wpływu na rehabilitację", "B. Objawy zapalenia pozagałkowego", "C. Zespół zaniedbywania połowiczego", "D. A i C prawidłowe"], c: 2 },
        { q: "6. Triada objawów w guzach mózgu obejmuje:", o: ["A. Bóle głowy", "B. Wymioty", "C. Tarcza zastoinowa na dnie oka", "D. Wszystkie wyżej wymienione"], c: 3 },
        { q: "7. Wskaż zdanie prawdziwe:", o: ["A. Udar mózgu najczęściej przebiega z silnym bólem głowy", "B. Udar niedokrwienny jest najczęstszym typem udaru mózgu", "C. Udar mózgu występuje wyłącznie powyżej 65 r.ż.", "D. Najczęstszym objawem jest zespół oponowy"], c: 1 },
        { q: "8. Nie szyje się ran:", o: ["A. Małych o przylegających brzegach", "B. Ran nie przekraczających głębokością czepca", "C. Ran starych i zanieczyszczonych", "D. Wszystkich wymienionych"], c: 3 },
        { q: "9. Do czynników niemodyfikowalnych udaru niedokrwiennego NIE należy:", o: ["A. Rasa", "B. Płeć", "C. Migotanie przedsionków", "D. Żadne z wymienionych"], c: 2 },
        { q: "10. Przed badaniem EEG pacjent powinien spożyć posiłek, ponieważ:", o: ["A. Spadki glukozy wpływają niekorzystnie na aktywność mózgu", "B. Pacjent lepiej zniesie badanie", "C. Spadki glukozy wpływają korzystnie", "D. Wszystkie powyższe"], c: 0 },
        { q: "11. Niedowład połowiczy prawostronny wskazuje, że uszkodzenie nastąpiło w:", o: ["A. Lewej półkuli", "B. Prawej półkuli", "C. Obu półkulach", "D. Prawidłowe odpowiedzi a i b"], c: 0 },
        { q: "12. Celem leczenia immunomodulacyjnego NIE jest:", o: ["A. Modyfikacja odpowiedzi immunologicznej", "B. Redukcja liczby rzutów", "C. Zmniejszenie nowych zmian demielinizacyjnych", "D. Wyleczenie choroby"], c: 3 },
        { q: "13. Do grupy leków przeciwpłytkowych (antyagregacyjnych) NIE należy:", o: ["A. Kwas acetylosalicylowy", "B. Tiklopidyna", "C. Klopidogrel", "D. Warfaryna"], c: 3 },
        { q: "14. W edukacji chorego z SM (leczenie interferonem) należy uważać na:", o: ["A. Objawy grypopodobne", "B. Reakcje skórne", "C. Wzrost enzymów wątrobowych", "D. Odpowiedzi A, B, C są prawidłowe"], c: 3 },
        { q: "15. Najpoważniejszym powikłaniem terapii trombolitycznej jest:", o: ["A. Objawowe krwawienie wewnątrzczaszkowe", "B. Nudności i wymioty", "C. Wysypka skórna", "D. Krwawienie z nosa"], c: 0 },
        { q: "16. Zalecając pacjentowi pozostanie na czczo do CT/MRI, pielęgniarka ma na uwadze:", o: ["A. Zapobieganie stresowi", "B. Zapobieganie objawom ubocznym po podaniu kontrastu", "C. Zapobieganie skutkom długotrwałego badania", "D. Zapobieganie wahaniom RR"], c: 1 },
        { q: "17. Do zasad podawania Actylise należą za wyjątkiem:", o: ["A. 10% w bolusie przez 2 minuty", "B. RR przez 2h mierzymy co 15 minut", "C. Po 24h należy wykonać CT", "D. Jeżeli stan wymaga, założyć sondę i cewnik przed lekiem"], c: 3 },
        { q: "18. Niedowład (paresis) jest to:", o: ["A. Całkowita niemożność ruchu", "B. Ograniczenie ruchu lub siły w wyniku braku bodźców nerwowych", "C. Ograniczenie w następstwie przykurczy", "D. Subiektywne uczucie zmniejszenia siły"], c: 1 },
        { q: "19. Ból głowy, niedowład, zaburzenia mowy trwające ok. 30 minut bez śladów ubytkowych to objawy:", o: ["A. Napadu ischemicznego", "B. Krwawienia podpajęczynówkowego", "C. Zatoru mózgu", "D. Krwotoku mózgowego"], c: 0 },
        { q: "20. Nierówność źrenic to:", o: ["A. Apraksja", "B. Anizokoria", "C. Afazja", "D. Agnozja"], c: 1 },
        { q: "21. W edukacji pacjenta z Parkinsonem przyjmującego lewodopę należy ograniczyć produkty:", o: ["A. Węglowodanowe", "B. Białkowe", "C. Tłuszczowe", "D. Witaminowe"], c: 1 },
        { q: "22. Afazja ruchowa polega na:", o: ["A. Utracie zdolności rozumienia mowy", "B. Utracie zdolności mówienia", "C. Trudności w nazywaniu przedmiotów", "D. Całkowitej niezdolności mówienia i rozumienia"], c: 1 },
        { q: "23. Stosowanie używek, zła dieta, otyłość, mała aktywność fizyczna to:", o: ["A. Niemodyfikowalne czynniki ryzyka", "B. Czynniki modyfikowalne chorób metabolicznych", "C. Czynniki modyfikowalne zależne od stylu życia", "D. Żadne z powyższych"], c: 2 },
        { q: "24. Odkleszczowe zapalenie mózgu to choroba:", o: ["A. Wirusowa", "B. Bakteryjna", "C. Grzybicza", "D. Żadna z wymienionych"], c: 0 },
        { q: "25. Borelioza to choroba:", o: ["A. Wirusowa", "B. Bakteryjna", "C. Grzybicza", "D. Żadna z wymienionych"], c: 1 },
        { q: "26. Jakie są działania niepożądane glikokortykosteroidów:", o: ["A. Spadek ciśnienia i obfita diureza", "B. Podrażnienie śluzówki żołądka, spadki ciśnienia", "C. Obfita diureza, osteoporoza, bezsenność", "D. Obrzęki, nadciśnienie, podrażnienie śluzówki żołądka, bezsenność"], c: 3 },
        { q: "27. Do przełomu cholinergicznego dochodzi w wyniku:", o: ["A. Zaawansowania miastenii", "B. Podaży zbyt dużych dawek leków", "C. Podaży zbyt małych dawek leków", "D. Prawidłowe odpowiedzi a i c"], c: 1 },
        { q: "28. Zapobieganie ortostatycznym spadkom ciśnienia w chorobie Parkinsona to:", o: ["A. Spanie w pozycji płaskiej", "B. Dieta ubogosolna", "C. Wstawanie z łóżka na 'trzy tempa'", "D. Ograniczenie płynów"], c: 2 },
        { q: "29. Czynnikiem wyzwalającym napady padaczkowe NIE jest:", o: ["A. Alkohol w dużych dawkach", "B. Długotrwałe przebywanie na słońcu", "C. Odpoczynek, regularny tryb życia, przyjmowanie leków", "D. Praca na komputerze, błyski świetlne"], c: 2 },
        { q: "30. Do powikłań urazów czaszkowo-mózgowych należą:", o: ["A. Wodogłowie pourazowe", "B. Padaczka pourazowa", "C. Zakażenia np. zapalenie opon", "D. Wszystkie wymienione"], c: 3 },
        { q: "31. Przygotowanie do badania wzrokowych potencjałów wywołanych polega na ZA WYJĄTKIEM:", o: ["A. Umyciu przed badaniem głowy", "B. Niestosowaniu lakierów/żeli do włosów", "C. Farmakologicznym rozszerzeniu źrenic", "D. Zabraniu okularów, jeśli pacjent ich używa"], c: 2 },
        { q: "32. Przeciwwskazania do badania MRI to:", o: ["A. Klips metalowy ferromagnetyczny tętniaka", "B. Metalowe ciało obce, rozrusznik serca", "C. Pierwszy trymestr ciąży, otyłość >130 kg, klaustrofobia", "D. Wszystkie odpowiedzi są prawidłowe"], c: 3 },
        { q: "33. Silny ból głowy, nudności, wymioty nasilające się przy pionizacji to objawy:", o: ["A. Zespołu popunkcyjnego", "B. Udaru mózgu", "C. Rzutu SM", "D. Wszystkie odpowiedzi są błędne"], c: 0 },
        { q: "34. Błędem postępowania podczas napadu padaczkowego NIE jest:", o: ["A. Izolowanie żuchwy z użyciem twardych przedmiotów", "B. Ochrona głowy przed urazami", "C. Budzenie ze snu ponapadowego", "D. Krępowanie ruchów ciała"], c: 1 },
        { q: "35. Insulina 80, strzykawka 2ml, podaj 24 j.m. Podana ilość to:", o: ["A. 0,3 ml", "B. 0,4 ml", "C. 0,6 ml", "D. 0,8 ml"], c: 0 },
        { q: "36. Insulina 100, strzykawka 2ml, podaj 30 j.m. Podana ilość to:", o: ["A. 0,3 ml", "B. 0,4 ml", "C. 0,6 ml", "D. 0,8 ml"], c: 0 },
        { q: "37. W przypadku ślinotoku stosuje się:", o: ["A. Częstą toaletę jamy ustnej", "B. Płukanie jamy ustnej środkami", "C. Zabezpieczenie skóry wokół ust", "D. Wszystkie wymienione"], c: 3 },
        { q: "38. Zaburzenia psychiczne, agresywność, wesołkowatość to objawy guza:", o: ["A. W płacie czołowym", "B. W płacie potylicznym", "C. W płacie skroniowym", "D. W przysadce mózgowej"], c: 0 },
        { q: "39. Zaznacz twierdzenie nieprawdziwe:", o: ["A. Nosić ciężary w obu rękach", "B. Dbać o prawidłową masę ciała", "C. Spać na miękkich materacach", "D. Nie wykonywać prac obciążających kręgosłup"], c: 2 },
        { q: "40. Spacery ukierunkowane to forma ćwiczeń:", o: ["A. Rehabilitacyjnych u pacjentów z chorobą Parkinsona", "B. Orientacji przestrzennej u pacjentów z otępieniem", "C. Ogólnokondycyjnych", "D. Żadne z wymienionych"], c: 1 },
        { q: "41. Wskaż najczęstszy objaw udaru mózgu:", o: ["A. Niedowład kończyn", "B. Uszkodzenie nerwu twarzowego obwodowego", "C. Bóle i zawroty głowy", "D. Afazja"], c: 0 },
        { q: "42. Czynnikiem sprzyjającym wystąpieniu odleżyn u chorych neurologicznie NIE jest:", o: ["A. Przeczulica", "B. Porażenie", "C. Niedowład", "D. Zaburzenia czynności zwieraczy"], c: 0 },
        { q: "43. Wskaż główne czynniki ryzyka udaru mózgu:", o: ["A. Wiek, nadciśnienie, choroba zwyrodnieniowa biodra", "B. Migotanie przedsionków, wiek, nadciśnienie tętnicze", "C. Cukrzyca, uraz głowy, depresja", "D. Depresja, Parkinson, nadciśnienie"], c: 1 },
        { q: "44. Najczęstsze przyczyny udaru mózgu to:", o: ["A. Miażdżyca tętnic szyjnych i migotanie przedsionków", "B. Miażdżyca naczyń i rozwarstwienie", "C. Migotanie, choroby metaboliczne", "D. Choroba małych naczyń, nikotynizm"], c: 0 },
        { q: "45. Najczęstszy typ zaburzeń mowy w przebiegu udaru mózgu to:", o: ["A. Anartria", "B. Dysfonia", "C. Mowa skandowana", "D. Afazja mieszana"], c: 3 },
        { q: "46. Otępienia korowe i podkorowe różni:", o: ["A. Czas rozwoju objawów", "B. Lokalizacja patologii", "C. Możliwa etiologia naczyniowa", "D. Wszystkie powyższe"], c: 3 },
        { q: "47. Do najczęstszych następstw udaru mózgu należą:", o: ["A. Zaburzenia węchu, depresja", "B. Padaczka, depresja", "C. Otępienie, uszkodzenie obwodowe n. twarzowego", "D. Niedosłuch, bóle głowy"], c: 1 },
        { q: "48. Do cech charakterystycznych neuropatii należy:", o: ["A. Osłabienie odruchów głębokich", "B. Wzmożenie odruchów głębokich", "C. Wzmożenie napięcia mięśniowego", "D. Występowanie bólu"], c: 0 },
        { q: "49. Wskaż odpowiedź NIEPRAWDZIWĄ 'łagodne zaburzenia poznawcze...':", o: ["A. Zawsze prowadzą do rozwoju otępienia", "B. Występują u 3-13% osób po 65 r.ż", "C. Brak uchwytnych klinicznie zaburzeń poznawczych", "D. Mogą pozostać w niezmiennym nasileniu do końca życia"], c: 0 },
        { q: "50. Męczliwość mięśni nasilająca się wieczorem. Pielęgniarka zwraca uwagę na:", o: ["A. Objawy niewydolności oddechowej zagrażającej życiu", "B. Dobre samopoczucie chorej", "C. Objawy niewydolności nerek", "D. Występowanie nudności, wymiotów"], c: 0 },
        { q: "51. Wskaż twierdzenie nieprawidłowe:", o: ["A. Pacjent z padaczką nie może być zawodowym kierowcą", "B. Pacjentka może urodzić zdrowe dziecko", "C. Pacjentka może karmić piersią", "D. Pacjent z padaczką może uprawiać sporty ekstremalne"], c: 3 },
        { q: "52. Brachialgia to:", o: ["A. Rwa udowa", "B. Rwa ramienna", "C. Rwa kulszowa", "D. Żadna z wymienionych"], c: 1 },
        { q: "53. Głównym celem ułożenia po nakłuciu lędźwiowym na brzuchu jest zapobieganie:", o: ["A. Unoszeniu głowy", "B. Wyciekowi płynu mózgowo-rdzeniowego przez miejsce wkłucia", "C. Nudnościom i wymiotom", "D. Bólowi głowy"], c: 1 },
        { q: "54. Objawem przełomu miastenicznego NIE jest:", o: ["A. Zwężenie źrenic", "B. Niepokój", "C. Tachykardia", "D. Rozszerzenie źrenic"], c: 0 },
        { q: "55. Podaż leków p/drgawkowych w stanie padaczkowym odbywa się:", o: ["A. Tylko drogą dożylną", "B. Tylko drogą doustną", "C. Tylko drogą domięśniową", "D. Drogą dożylną, domięśniową i doustną"], c: 0 },
        { q: "56. Dieta chorego na SM powinna uwzględniać:", o: ["A. Większą podaż węglowodanów prostych", "B. Zmniejszoną ilość płynów", "C. Zwiększoną podaż wielonienasyconych kwasów omega", "D. Wszystkie powyższe"], c: 2 },
        { q: "57. Postępowanie pielęgniarskie przy opadających powiekach u chorych na miastenię:", o: ["A. Okulary również w oświetlonych pomieszczeniach", "B. Naprzemienne zaklejanie oka, odpoczynek z zamkniętymi oczami", "C. Podklejanie opadających powiek", "D. Wszystkie odpowiedzi prawidłowe"], c: 3 },
        { q: "58. Dieta chorego na miastenię powinna zawierać:", o: ["A. Zwiększoną podaż sodu", "B. Zwiększoną podaż białka", "C. Zwiększoną podaż potasu", "D. Zwiększoną podaż węglowodanów"], c: 2 },
        { q: "59. Prawidłowa pozycja w ostrym udarze niedokrwiennym:", o: ["A. Pozycja Trendelenburga", "B. Pozycja półsiedząca", "C. Pozycja grzbietowa z głową uniesioną o około 20 stopni", "D. Pozycja z opuszczonymi nogami"], c: 2 }
    ];

    const questionEl = document.getElementById('question');
    const optionsEl = document.getElementById('options');
    const submitBtn = document.getElementById('submit');
    const resultEl = document.getElementById('result');
    const progressEl = document.getElementById('progress');

    let currentQuiz = 0;
    let score = 0;
    let answered = false;

    function loadQuiz() {
        answered = false;
        submitBtn.innerText = "Sprawdź odpowiedź";
        optionsEl.innerHTML = '';
        const currentData = quizData[currentQuiz];
        
        progressEl.innerText = `Pytanie ${currentQuiz + 1} z ${quizData.length}`;
        questionEl.innerText = currentData.q;

        currentData.o.forEach((option, index) => {
            const label = document.createElement('label');
            label.classList.add('option');
            label.innerHTML = `<input type="radio" name="answer" value="${index}"> ${option}`;
            optionsEl.appendChild(label);
        });
    }

    function getSelected() {
        let answer;
        document.querySelectorAll('input[name="answer"]').forEach(el => {
            if(el.checked) answer = el.value;
        });
        return answer;
    }

    submitBtn.addEventListener('click', () => {
        if (!answered) {
            const answer = getSelected();
            if (answer !== undefined) {
                answered = true;
                const correctIndex = quizData[currentQuiz].c;
                const labels = document.querySelectorAll('.option');
                
                // Kolorowanie odpowiedzi
                labels[correctIndex].classList.add('correct');
                if (Number(answer) === correctIndex) {
                    score++;
                } else {
                    labels[answer].classList.add('wrong');
                }
                
                submitBtn.innerText = "Następne pytanie";
            } else {
                alert("Proszę wybrać odpowiedź!");
            }
        } else {
            currentQuiz++;
            if (currentQuiz < quizData.length) {
                loadQuiz();
            } else {
                document.getElementById('quiz').style.display = 'none';
                let perc = Math.round((score / quizData.length) * 100);
                resultEl.innerHTML = `Egzamin zakończony!<br><br> Twój wynik: ${score} / ${quizData.length} (${perc}%)<br><br> <button onclick="location.reload()">Rozwiąż ponownie</button>`;
            }
        }
    });

    loadQuiz();
</script>

</body>
</html>
