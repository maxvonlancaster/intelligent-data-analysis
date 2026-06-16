# Інтелектуальний Аналіз Даних 

Навальний посібник 

Матвій О. В. 
Мельник В. С.
Мельник Г. В.

Чернівецький Національний Університет, 2026

## 1. Збір даних (Data Collection)

Першим кроком більшості процесів обробки даних є отримання даних. Дані, які ми зазвичай використовуємо, походять з багатьох різних джерел.

Якщо вам пощастить, хтось може безпосередньо надати вам файл, наприклад CSV. А іноді для збору відповідних даних потрібно буде виконати запит до бази даних . Але в цій лекції ми поговоримо про збір даних з двох основних джерел:

- запит до API (більшість з яких сьогодні є веб-орієнтованими); та
- вилучення даних з веб-сторінки.

**Збір даних з веб-джерел (Collecting data from web-based sources)**

Переважна більшість автоматизованих запитів даних, які ви будете виконувати, використовуватимуть HTTP-запити (це стало домінуючим протоколом не тільки для запитів веб-сторінок).

```
import requests
response = requests.get("https://fmi.chnu.edu.ua/")

print("Status Code:", response.status_code)
print("Headers:", response.headers)

print(response.text)
```

HTTP GET є найпоширенішим методом, але існують також методи PUT, POST, DELETE, які змінюють деякі параметри на сервері.

Параметри URL можна вказати за допомогою бібліотеки запитів, як показано нижче:

```
params = {"sa":"t", "rct":"j", "q":"", "esrc":"s",
"source":"web", "cd":"9", "cad":"rja", "uact":"8"}
response = requests.get("http://www.google.com/url", params=params)

print("Final URL:", response.url)
print(response.text)
```

**RESTful API**

Якщо ви перейдете від простого запиту веб-сторінок до веб-API, ви, швидше за все, зіткнетеся з REST API (Representational State Transfer). REST — це скоріше архітектура дизайну, але ось кілька ключових моментів:

Використовує стандартний інтерфейс і методи HTTP (GET, PUT, POST, DELETE)
Stateless – сервер не запам'ятовує, що ви робили
Практичне правило: якщо ви надсилаєте ключ свого облікового запису разом із кожним викликом API, ви, ймовірно, використовуєте REST API

Ви запитуєте REST API подібно до стандартних HTTP-запитів, але майже завжди потрібно включати параметри

Отримайте власний токен доступу на https://github.com/settings/tokens/new GitHub API використовує GET/PUT/DELETE, щоб ви могли автоматично запитувати або оновлювати елементи у вашому обліковому записі GitHub Приклад REST: сервер не запам'ятовує ваші останні запити, наприклад, ви завжди повинні включати свій токен доступу, якщо використовуєте його таким чином

```
import os
from dotenv import load_dotenv

load_dotenv()
token = os.getenv("GITHUB_TOKEN")

headers = {'Authorization': 'Bearer '+token}
response = requests.get("https://api.github.com/user", headers=headers)
print(response.content)
```

**CSV-файли**

CSV: Відноситься до будь-якого текстового файлу з роздільниками (не завжди розділеного комами).

Якщо самі значення містять коми, ви можете взяти їх у лапки (наш реєстратор, очевидно, завжди так робить, щоб бути впевненим).


```
import pandas as pd

dataframe = pd.read_csv("../resources/bitcoin.csv", delimiter=',', quotechar='"')
dataframe.head(10)
```

**Парсинг JSON в Python**

Вбудована бібліотека для читання/запису об'єктів Python з/у файли JSON

```
import json
# load json from a REST API call

headers = {'Authorization': 'token ' + token}
response = requests.get("https://api.github.com/user", headers=headers)
data = json.loads(response.content)

#json.load(file) # load json from file
json.dumps(data) # return json string
#json.dump(obj, file) # write json to file
```

**Парсинг XML/HTML в Python**

Існує ряд XML/HTML-парсерів для Python, але для науки про дані добре підходить бібліотека BeautifulSoup (спеціально призначена для вилучення даних з XML/HTML-файлів).

```
# get all the links within the webpage
from bs4 import BeautifulSoup
import requests

response = requests.get("https://fmi.chnu.edu.ua/")

root = BeautifulSoup(response.content)
root.find("div").findAll("a")
```

**Регулярні вирази (Regular expressions)**

Після завантаження даних (або якщо вам потрібно створити парсер для завантаження даних іншого формату) часто доводиться шукати певні елементи в даних.

Наприклад, знайти перше входження рядка «data science»

```
import re
text = "This course will introduce the basics of data science"
match = re.search(r"data science", text)
print(match.start())
print(re.match(r"This", text))

# Regular expressions in Python

match = re.match(r"data science", text) # check if start of text matches
match = re.search(r"data science", text) # find first match or None

all_matches = re.findall(r"a", text) # return all matches
print(all_matches)
```

**Відповідність декількох потенційних символів (Matching multiple potential characters)**

Справжня сила регулярних виразів полягає в можливості збігу декількох можливих послідовностей символів. Спеціальні символи в регулярних виразах: `.^$*+?{}\[]|()` (якщо ви хочете, щоб ці символи збігалися точно, вам потрібно їх екранувати: `\$`)

Відповідність наборів символів:

- Відповідність символу «a»: `a`
- Відповідність символів «a», «b» або «c»: `[abc]`
- Будь-який символ, крім «a», «b» або «c»: `[^abc]`
- Відповідність будь-якої цифри: `\d` (те саме, що `[0-9]`)
- Відповідність будь-якого алфавітно-цифрового символу: `\w` (те саме, що `[a-zA-z0-9_]`)
- Відповідність пробілу: `\s` (те саме, що `[ \t\n\r\f\v]`)
- Відповідність будь-якого символу: `.` (включно з новою лінією з re.DOTALL)

Може збігатися з одним або декількома випадками символу (або набору символів)

Деякі поширені модифікатори:

- Відповідність символу «a» рівно один раз: `a`
- Відповідність символу «a» нуль або один раз: `a?`
- Відповідність символу «a» нуль або більше разів: `a*`
- Відповідність символу «a» один або більше разів: `a+`
- Відповідність символу «a» рівно n разів: `a{n}`

Можна поєднувати їх із збігом декількох символів:

- Відповідність усіх випадків «`<something>` science», де `<something>` — це алфавітно-цифровий рядок, що містить принаймні один символ:
`\w+\s+science`


## 2. Data Processing

**Pandas**

Існує ряд бібліотек Python, які обробляють реляційні дані, зазвичай написані як інтерфейси до декількох різних систем управління реляційними базами даних (таких як PostgreSQL, MySQL та інші). [Примітка: слід зазначити, що програмне забезпечення, таке як PostreSQL і MySQL, правильніше називати системами управління реляційними базами даних (RDBMS), а не базами даних. База даних — це фактичні таблиці та записи, що визначають фактичну сукупність даних.

Однак у цьому курсі ми будемо працювати з реляційними даними переважно за допомогою двох бібліотек: Pandas і SQLite. Це особливо прості бібліотеки, якщо говорити про реальні бази даних: Pandas, безумовно, не є реальною системою реляційних баз даних (хоча вона надає функції, що віддзеркалюють деякі їхні можливості), тоді як SQLite є «реальною» RDBMS, але надзвичайно простою, без стандартної архітектури клієнт/сервер, яка є практично у всіх реальних виробничих базах даних. Проте для багатьох задач у галузі науки про дані вони будуть достатніми, тому ми зосередимося на них тут.

Ми вже коротко розглянули Panda, коли обговорювали збір даних, і вона виявилася однією з найкорисніших бібліотек Python для науки про дані. Як ми вже згадували вище (але будемо повторювати цей факт багато разів), Pandas — це не бібліотека реляційних баз даних, а бібліотека «даних-рам». Ви можете уявити собі фрейм даних як 2D-масив, за винятком того, що записи у фреймі даних можуть бути будь-яким типом об'єкта Python (і мати змішані типи в масиві), а рядки/стовпці можуть мати «мітки» замість цілочисельних індексів, як у стандартному масиві.

Давайте подивимося, як спочатку створити фрейм даних у Pandas, який відображає нашу таблицю Person вище (ми залишимо стовпець «Role ID» поза увагою, щоб спростити завдання).



## 3. Візуалізація даних (Data visualization)

Важливо підкреслити різницю між двома дуже різними поняттями, які люди описують, використовуючи загальний термін «візуалізація». Перше з них — «візуалізація для дослідження даних»: використання візуалізації для розуміння (тобто для власного розуміння) сукупності даних з метою подальшого аналізу. Коротко кажучи, мета цього типу візуалізації — «знайти істину» в даних. Альтернативою є «візуалізація для презентації»: виокремлення певного аспекту даних у вигляді зрозумілої діаграми або графіку, що передає широкій аудиторії конкретний аспект даних, який ви хочете донести. Цей тип візуалізації має на меті «переконати інших людей у правдивості ваших висновків».

*Візуалізація VS статистика:* Візуалізація майже завжди надає більш інформативний (хоча і менш кількісний) огляд ваших даних, ніж статистика (підхід, а не галузь науки в цілому).

**Типи даних (Types of data)**

Чотири основні типи даних:

- *Номінальні дані (Nominal data)*: категоріальні дані без внутрішнього порядку між категоріями. Наприклад, змінна «тип домашньої тварини» може складатися з класів {собака, кіт, кролик}, і між цими двома типами немає відносного порядку, це просто різні дискретні значення.

- *Порядкові дані (Ordinal data)*: категоріальні дані з внутрішньою послідовністю, але «відмінності» між категоріями не мають суто числового значення. Канонічним прикладом тут є відповіді на опитування з такими відповідями: {категорично не згоден, трохи не згоден, нейтральний, трохи згоден, категорично згоден}. Важливою особливістю тут є те, що, хоча між цими типами існує чітка послідовність, немає сенсу вважати, що різниця між «трохи згоден» і «категорично згоден» є «такою ж», як різниця між «нейтральний» і «трохи згоден».

- *Інтервальні дані (Interval data)*: числові дані, тобто дані, які можна відобразити на «числовій прямій»; важливим аспектом на відміну від порядкових даних є не «дискретна проти безперервної диференціації» (наприклад, цілочисельні значення можна вважати інтервальними даними), а той факт, що відносні відмінності в інтервальних даних мають значення. Класичним прикладом є температура (у Фаренгейті або Цельсії, що ми підкреслимо трохи пізніше): тут різниці між температурами мають значення: 10 і 15 градусів розділені такою ж величиною, як 15 і 20 (ця властивість настільки властива числовим даним, що майже дивно її підкреслювати). З іншого боку, інтервальні дані охоплюють випадки, коли нульова точка «не має реального значення»; на практиці це означає, що співвідношення між двома точками даних не має значення. Двадцять градусів за Фаренгейтом не є «вдвічі гарячішими» в будь-якому значущому сенсі, ніж 10 градусів; і, звичайно, не нескінченно гарячішими, ніж нуль градусів.

- *Дані про співвідношення (Ratio data)*: також числові дані, але співвідношення між вимірами має певне значення. Класичним прикладом тут є температура за Кельвіном. Очевидно, що, як і температура за Фаренгейтом або Цельсієм, це описує основне явище температури, але на відміну від попередніх випадків, нуль за Кельвіном має значення з точки зору молекулярної енергії в речовині (тобто її відсутність). Це означає, що співвідношення мають реальне значення: речовина при 20 градусах Кельвіна має вдвічі більше кінетичної енергії на молекулярному рівні, ніж та сама речовина при 10 градусах Кельвіна.

«Допустимі операції», які ми можемо виконувати над двома даними різних типів:

Номінальні дані: =,≠. Все, що ми можемо зробити з номінальними даними, це порівняти два дані та перевірити, чи вони рівні.

Порядкові дані: =,≠,<,>. Окрім перевірки на рівність, ми також можемо порівнювати порядок різних точок даних.

Інтервальні дані: =,≠,<,>,−. Ми маємо всі операції з порядковими даними, але також можемо обчислювати точні числові різниці між двома точками даних. Залежно від контексту, ми можемо сказати, що додавання також дозволено, але іноді додавання фактично передбачає нульову точку і тому застосовується тільки до даних про співвідношення.

Дані співвідношення: =,≠,<,0,−,+,÷. Ми маємо всі операції з інтервальними даними, але додавання тепер практично завжди дозволено, і ми можемо додатково виконувати ділення (для визначення співвідношень) між різними точками даних.

**Коротко**:

Номінальні: категоріальні дані, без упорядкування Приклад – Домашні тварини: {собака, кіт, кролик, …} Операції: =, ≠

Порядкові: категоріальні дані, з упорядкуванням Приклад – Рейтинг: {«поганий»,«нейтральний»,“хороший”,«дуже хороший»} Операції: =, ≠, ≥, ≤, >, <

Інтервал: числові дані, нуль не означає нульову «кількість» Приклад – температура за Фаренгейтом, Цельсієм Операції: = , ≠, ≥, ≤, >, <, +, −

Співвідношення: числові дані, нуль має значення, пов'язане з нульовою «кількістю» Приклад – температура за Кельвіном Операції: = , ≠, ≥, ≤, >, <, +, −,÷

**Побудова графіків різних типів даних**

Щоб бути більш формальними, далі ми припускаємо, що маємо доступ до набору даних, який можна позначити як

$\{x^{(1)},x^{(2)},…,x^{(m)}\}$

де кожне $x^{(i)}$ є деякою точкою даних. $x^{(i)}=(a_{1}^{i}, a_{2}^{i}, a_{3}^{i}, ...)$

Для побудови всіх цих графіків ми будемо використовувати бібліотеку matplotlib, яка добре інтегрується з Jupyter notebook.

**1D дані**

**Стовпчасті діаграми (Bar charts) — категоріальні дані**

Якщо ваші дані є одновимірними категоріальними, єдиним реальним варіантом для їх візуалізації є використання стовпчастої діаграми. Зверніть увагу, що, виходячи з нашого припущення про те, що порядок точок даних у нашому наборі даних не має значення, єдиною значущою інформацією, якщо ми маємо набір даних типу

$\{student,teacher,student,professor,teacher,student,…\}$

буде кількість повторень кожного елемента. Таким чином, ми можемо ефективно узагальнити дані, включивши кількість повторень кожного значення даних (це легко зробити за допомогою класу `collections.Counter`).

Примітка: незважаючи на те, що ви, можливо, бачили на деяких рисунках, зверніть увагу, що включення ліній між різними стовпчиками однозначно не має сенсу.

```
import collections
import matplotlib.pyplot as plt
import numpy as np

data = np.random.permutation(np.array(["student"]*15 + ["teacher"]*7 + ["professor"]*3))
# print(data)
counts = collections.Counter(data)
plt.bar(range(len(counts)), list(counts.values()), tick_label=list(counts.keys()))
```

![barchart](./img/103-1.png)

**Кругові діаграми (Pie charts) — просто скажіть «ні»**

Кругові діаграми призначені для представлення даних, а не для їх дослідження!

Люди не дуже добре оцінюють площу або кути (наприклад, важко визначити, чи один сегмент становить 23 %, а інший — 27 %). Стовпчасті діаграми набагато простіші, оскільки люди точніше порівнюють довжини.

Порівняти дві різні кругові діаграми (наприклад, два періоди часу) майже неможливо. Неможливо легко додати смуги похибки, опорні лінії або точні порівняння.

Ось приклад того, як побудувати кругову діаграму:

```
data = {"strongly disagree": 3,
        "disagree": 8,
        "neutral": 12,
        "agree": 11,
        "strongly agree": 4}

plt.pie(data.values(), labels=data.keys(), autopct='%1.1f%%')
plt.axis('equal')
```

![piechart](./img/103-2.png)



**Гістограми (Histograms) — числові дані**

```
np.random.seed(0)
data = np.concatenate([30 + 4*np.random.randn(5000),
                       22 + 2*np.random.randn(7000),
                       12 + 3*np.random.randn(3000)])
plt.hist(data, bins=50)


# data = np.concatenate([10 + 5*np.random.randn(10000)])
# plt.hist(data, bins=50);
```

![histogram](./img/103-3.png)


**2D дані**

**Точкові діаграми — числові x числові (Scatter plots — numeric x numeric)**

Якщо обидва виміри даних є числовими, найприроднішим першим типом діаграми, який слід розглянути, є точкова діаграма: нанесення точок, які просто відповідають різним координатам даних. (Діаграма розсіювання)

```
x = np.random.randn(1000)
y = 0.8*x**3 + x + 1.5*np.random.randn(1000)
plt.scatter(x,y,s=1)
```

![scatter](./img/103-4.png)


Однак існує також природний режим відмови, коли точок занадто багато, щоб їх можна було чітко розділити, і графік втрачає здатність показувати щільність даних. Наприклад, якщо у нас в 10 разів більше точок, графік вже не так чітко показує щільність у внутрішній частині «щільності».

У цьому випадку надлишку даних ми також можемо створити 2D гістограму даних (яка групує дані за обома вимірами) і вказати «висоту» кожного блоку за допомогою кольорової карти. Такі графіки можуть чіткіше вказувати щільність точок у регіонах, які в оригінальному графіку розсіювання мають суцільний колір. Ці 2D гістограми іноді також називають *тепловими картами* (heat maps), але ця назва часто конфліктує з подібними версіями, що використовуються для побудови 2D категоріальних даних, тому тут ми будемо використовувати термін *2D гістограма*.

```
x = np.random.randn(10000)
y = 0.8*x**3 + x + 1.5*np.random.randn(10000)
#plt.scatter(x,y,s=10)

plt.hist2d(x,y,bins=100);
plt.colorbar();
plt.set_cmap('hot')
```

![heat](./img/103-5.png)



**Лінійна діаграма (line plot) - numeric x numeric (sequential)**


```
x = np.linspace(0,10,1000)
y = np.cumsum(0.01*np.random.randn(1000))
plt.plot(x,y)
```



![line](./img/103-6.png)


**Коробкова (Box and whiskers) і скрипкові діаграми (Violin plots) — категоріальні x числові**

Розглянемо простий приклад, де (тільки з вигаданими даними) ми будуємо графік учасників навчального процесу та їхній вік.

```
import numpy as np
import matplotlib.pyplot as plt
import scipy.linalg as la

data= {"student": 22 + 1.4*np.random.randn(1000),
       "teacher": 37 + 2.5*np.random.randn(900),
       "professor": 65 + 5.5*np.random.randn(300)}
plt.scatter(np.concatenate([i*np.ones(len(x)) for i,x in enumerate(data.values())]),
            np.concatenate(list(data.values())))
plt.xticks(range(len(data)), data.keys());
```

![line](./img/103-7.png)


Очевидно, що, дивлячись тільки на цей графік, можна визначити дуже мало, оскільки в щільній лінії точок недостатньо інформації, щоб дійсно зрозуміти розподіл числової змінної для кожної точки. Поширеною стратегією тут є використання графіка коробкова, або коробка з вусами (box and whiskers), яка відображає медіану даних (як лінію в середині коробки), 25-й і 75-й процентилі даних (як нижня і верхня межі коробки), «вуса» встановлюються за допомогою ряду різних можливих угод (за замовчуванням Matplotlib використовує 1,5-кратний інтерквартильний розмах, відстань між 25-м і 75-м процентилем), а будь-які точки поза цим діапазоном («випадкові значення») відображаються окремо.

```
plt.boxplot(data.values())
plt.xticks(range(1,len(data)+1), data.keys())
```

![line](./img/103-8.png)


Звичайно, так само як середні значення та стандартні відхилення не описують повністю набір даних, статистика бокс-плат і вусів не відображає повністю розподіл даних. З цієї причини також часто використовуються скрипкові діаграми, які створюють міні-гістограми.

```
plt.violinplot(data.values())
plt.xticks(range(1,len(data)+1), data.keys())
```

![violin](./img/103-9.png)



**Теплова карта (Heatmap) та бульбашкові діаграми (Bubble plots) — категоріальні x категоріальні**

Коли обидва виміри наших 2D-даних є категоріальними, ми маємо ще менше інформації для використання. Знову ж таки, метою буде надання певної інформації про загальну кількість усіх можливих комбінацій між двома наборами даних. Наприклад, розглянемо вигаданий набір даних про учасників навчального процесу та їхній улюблений спосіб навчання

```
types = np.array([('student', 'offline'), ('student', 'remote'), 
                  ('teacher', 'offline'), ('teacher', 'remote'), 
                  ('professor', 'offline'), ('professor', 'remote')])
data = types[np.random.choice(range(6), 2000, p=[0.4, 0.1, 0.12, 0.18, 0.05, 0.15]),:]

# print(data[100:])

label_x, x = np.unique(data[:,0], return_inverse=True)
label_y, y = np.unique(data[:,1], return_inverse=True)
M, xt, yt, _ = plt.hist2d(x,y, bins=(len(label_x), len(label_y)))
plt.xticks((xt[:-1]+xt[1:])/2, label_x)
plt.yticks((yt[:-1]+yt[1:])/2, label_y)
plt.colorbar()
```

![heat](./img/103-10.png)


Хоча це може бути дещо корисним, діапазон кольорів, безперечно, не є дуже інформативним у деяких налаштуваннях, тому більш доречним може бути діаграма розсіювання з розмірами, пов'язаними з кожним типом даних (це також називається бульбашковою діаграмою).

```
xy, cnts = np.unique((x,y), axis=1, return_counts=True)
plt.scatter(xy[0], xy[1], s=cnts*5)
plt.xticks(range(len(label_x)), label_x)
plt.yticks(range(len(label_y)), label_y)
```

![heat](./img/103-11.png)

**3D дані**

Як тільки ми виходимо за межі двох вимірів, ефективна візуалізація стає набагато складнішою.

3D точкові графіки (3D scatter plots): cлід уникати, якщо не можна взаємодіяти з 3D-графіком:

```
x = np.random.randn(1000)
y = 0.4*x**3 + x + 1.7*np.random.randn(1000)
z = 0.5 + 0.1*(y-1)**2 + 0.4*np.random.randn(1000)

from mpl_toolkits.mplot3d import Axes3D
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.scatter(x,y,z)
```

![heat](./img/103-12.png)


**Матриці розсіювання (точкові матриці) (scatter matrices)**

Універсальним засобом для візуалізації високорозмірних даних є матриця розсіювання.

Щодо матриць розсіювання слід зазначити один важливий момент: не намагайтеся використовувати їх для представлення даних.


```
import pandas as pd
df = pd.DataFrame([x,y,z]).transpose()
pd.plotting.scatter_matrix(df, hist_kwds={'bins':50})
```

![heat](./img/103-13.png)


**Бульбашкові діаграми (Bubble plots)**

Бульбашкова діаграма з попереднього розділу також може бути одним із засобів розуміння тривимірних даних. У цій діаграмі та її варіантах ми побудуємо двовимірну точкову діаграму, але додамо третій вимір за допомогою іншого атрибуту, наприклад розміру точок. Для наведених вище даних це буде виглядати наступним чином.

```
plt.scatter(x,y,s=z*20)
```

![heat](./img/103-14.png)



```
plt.scatter(x,y,c=z)
```

![heat](./img/103-15.png)

## 4. Лінійна алгебра та бібліотека NumPy

- Вектори та матриці відіграють центральну роль в інтелектуальному аналізі даних.
- Матриці - очевидний спосіб зберігання табличних даних.
- Основа лінійної алгебри, яка є мовою всіх алгоритмів аналізу даних (ml, ai тощо).



## 5. Теорія Графів 

## 6. Обробка Природної Мови (Natural Language Processing, NLP)

Велика кількість даних у багатьох реальних наборах даних представлена у вигляді так званого вільного тексту (free text). Це є, наприклад, коментарі користувачів, телеметрія програмних додатків, будь-які «неструктуровані» поля, та багато чого іншого.

Обчислювальна обробка природної мови полягає в тому, що ми пишемо комп'ютерні програми, які можуть розуміти природну мову. Обробка природної мови (NLP) займається тим, як перетворити текст — часто нечіткий і непідготовлений — у форму, зручну для аналізу та моделювання. В цьому розділі ми спробуємо отримати деяку значущу інформацію з неструктурованих текстових даних.

Першим кроком до роботи з такими даними зазвичай стає підготовка тексту: приведення символів до одного регістру, видалення розділових знаків і зайвих символів, очищення чисел і зведення виробленого шуму у більш стандартизований вигляд.

Приклад очищення тексту (clean_text):
```python
import re
import string
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
import nltk

# Завантаження ресурсів NLTK (виконується один раз)
nltk.download('stopwords')
nltk.download('punkt')

def clean_text(text):
        # Перевести в нижній регістр
        text = text.lower()

        # Видалити розділові знаки
        text = text.translate(str.maketrans('', '', string.punctuation))

        # Видалити числа
        text = re.sub(r'\d+', '', text)

        # Видалити спеціальні символи (залишаємо лише англійські літери та пробіли)
        text = re.sub(r'[^a-z\s]', '', text)

        # Видалити стоп-слова
        stop_words = set(stopwords.words('english'))
        text = ' '.join(word for word in word_tokenize(text) if word not in stop_words)

        return text

sample_text = "Hello! This is an example text with numbers 123 and punctuation!!!"
cleaned_text = clean_text(sample_text)
print(cleaned_text)  # очікуваний вивід: hello example text numbers
```

Після базового очищення йде токенізація — розбиття тексту на слова або речення, що дозволяє оперувати окремими «будівельними блоками» змісту. Далі часто застосовують стемінг або лематизацію: стемінг обрізає закінчення й повертає корінь слова, іноді утворюючи несловникові форми, тоді як лематизація намагається відновити словникову форму з урахуванням контексту. Нормалізація може також включати виправлення орфографії, розгортання абревіатур і узгодження синонімів — усе задля того, щоб одна й та сама ідея не розпадалася на багато еквівалентних представлень.

Токенізація (word/sentence tokenization):
```python
from nltk.tokenize import word_tokenize, sent_tokenize

def tokenize_text(text):
        words = word_tokenize(text)
        sentences = sent_tokenize(text)
        return words, sentences

sample_text = "Natural Language Processing (NLP) is exciting! It enables computers to understand human language."
words, sentences = tokenize_text(sample_text)
print('Words:', words)
print('Sentences:', sentences)
```

Стемінг та лематизація:
```python
from nltk.stem import PorterStemmer, WordNetLemmatizer
from nltk.tokenize import word_tokenize
import nltk

# Завантаження словника для лематизації
nltk.download('wordnet')

def apply_stemming(text):
        stemmer = PorterStemmer()
        words = word_tokenize(text)
        stemmed_words = [stemmer.stem(word) for word in words]
        return stemmed_words

def apply_lemmatization(text):
        lemmatizer = WordNetLemmatizer()
        words = word_tokenize(text)
        lemmatized_words = [lemmatizer.lemmatize(word) for word in words]
        return lemmatized_words

sample_text = "The children are playing with their toys."
print('Stemmed:', apply_stemming(sample_text))
print('Lemmatized:', apply_lemmatization(sample_text))
```

Нормалізація (спрощені приклади: виправлення орфографії, абревіатури):
```python
from nltk.tokenize import word_tokenize
from autocorrect import Speller
import nltk

nltk.download('punkt')

def normalize_text(text):
        spell = Speller(lang='en')
        normalization_dict = {
                "u": "you",
                "ur": "your",
                "r": "are",
                "pls": "please",
                "thx": "thanks"
        }
        words = word_tokenize(text.lower())
        normalized_words = [normalization_dict.get(word, spell(word)) for word in words]
        normalized_text = ' '.join(normalized_words)
        normalized_text = normalized_text.replace('!', '').replace('.', '').strip()
        return normalized_text

sample_text = "U r the best! Pls help me with this assignment. Thx!"
print(normalize_text(sample_text))
```

Після підготовки тексту його потрібно представити чисельно. Простий і часто ефективний підхід — модель «мішка слів», при якій кожен документ кодується вектором частот слів. Щоб зменшити вплив дуже частих, але малоінформативних слів, використовують вагу зворотної частоти документа: $idf_{i}=\log\left(\dfrac{N_{documents}}{N_{documents\text{-}with\text{-}word\_i}}\right)$. Поєднання частоти терміна та зворотної частоти документа дає відому міру TF–IDF: $tfidf_{i,j}=tf_{i,j}\times idf_{i}$, яка підсилює рідкісні й інформативні слова й послаблює надто загальні.

Генерація wordcloud (приклад збору тексту зі сторінки та візуалізації):
```python
from wordcloud import WordCloud
from bs4 import BeautifulSoup
import requests
import re

response = requests.get("https://mathmod.chnu.edu.ua/")
root = BeautifulSoup(response.content, "lxml")
wc = WordCloud(width=800, height=400).generate(re.sub(r"\s+", " ", root.text))
wc.to_image()
```

![ml](./img/106-1.png)


TF (bag-of-words) — приклад побудови словника та матриці частот:
```python
documents = [
        "In Inception, the plot unfolds with thrilling action, keeping audiences on edge through a series of intense and unexpected twists.",
        "The story of Inception keeps audiences hooked with its thrilling action and surprising twists, making every moment unforgettable.",
        "The Matrix transformation preserves the vector's direction, illustrating how linear combinations affect the overall system."
]

document_words = [doc.lower().split() for doc in documents]
vocab = sorted(set(sum(document_words, [])))
vocab_dict = {k: i for i, k in enumerate(vocab)}

import numpy as np
X_tf = np.zeros((len(documents), len(vocab)), dtype=int)
for i, doc in enumerate(document_words):
        for word in doc:
                X_tf[i, vocab_dict[word]] += 1

print(X_tf)
```

Косинусна подібність — простий спосіб виміряти схожість між двома векторними представленнями тексту: $CosineSim(x,y)=\dfrac{x\cdot y}{\|x\|_{2}\|y\|_{2}}$. Вона добре працює для порівняння документів у просторі TF–IDF, але має й обмеження: в такому підході немає розуміння семантики слів, і різні слова, які мають близьке значення, залишаються віддаленими в просторі one‑hot або частотних векторів.

Щоб виправити цю проблему, використовують вбудовування слів (word embeddings) — щільні вектори фіксованого розміру, у яких семантично близькі слова займають близькі позиції у просторі. Моделі на кшталт word2vec або переднавчені матриці типу GloVe навчають векторні представлення таким чином, що арифметичні операції над векторами можуть відображати мовні відношення: наприклад, операція \(\text{king} - \text{man} + \text{woman}\) зазвичай наближається до вектора, близького до \(\text{queen}\). Це відкриває шлях до більш змістовних порівнянь і до знаходження синонімів, антонімів і тематичних зв'язків.

IDF, TF–IDF та косинусна подібність:
```python
idf = np.log(X_tf.shape[0] / X_tf.astype(bool).sum(axis=0))
X_tfidf = X_tf * idf
X_tfidf_norm = X_tfidf / np.linalg.norm(X_tfidf, axis=1)[:, None]
M = X_tfidf_norm @ X_tfidf_norm.T
print('IDF:', idf)
print('TF-IDF matrix:\n', X_tfidf)
print('Cosine similarity matrix:\n', M)
```

Word embeddings (GloVe через gensim.downloader) та приклад аналогій:
```python
import gensim.downloader as api
word_vectors = api.load("glove-wiki-gigaword-100")
vector = word_vectors['computer']
print('vector sample:', vector[:10])
print('similarity woman/man:', word_vectors.similarity('woman', 'man'))

king = word_vectors['king']
man = word_vectors['man']
woman = word_vectors['woman']
result_vector = king - man + woman
print('Analogy results:')
for word, similarity in word_vectors.similar_by_vector(result_vector, topn=5):
        print(word, similarity)
```

TF–IDF і схожість у Gensim (корпус, словник, модель TFIDF, MatrixSimilarity):
```python
import gensim as gs
from gensim.test.utils import lee_corpus_list
from gensim.models import Word2Vec

documents = [
        "kyiv has some excellent new restaurants",
        "kharkiv is a city with great cuisine",
        "postgresql is a relational database management system"
]
document_words = [doc.split() for doc in documents]
dictionary = gs.corpora.Dictionary(document_words)
corpus = [dictionary.doc2bow(doc) for doc in document_words]
tfidf = gs.models.TfidfModel(corpus)
X_tfidf = gs.matutils.corpus2csc(tfidf[corpus])
print(X_tfidf.todense().T)

M = gs.similarities.MatrixSimilarity(tfidf[corpus])
print(M.get_similarities(tfidf[corpus]))
```

Далі стоять мовні моделі, які намагаються оцінити ймовірність слова, враховуючи контекст: $P(word_{i}\mid word_{1},\dots,word_{i-1})$. Простий, але корисний клас таких моделей — n‑грами, що враховують обмежений контекст попередніх $n-1$ слів; їх можна вважати аппроксимацією повної ймовірнісної моделі мови. Сучасні нейронні та трансформерні моделі значно перевершують n‑грами за здатністю захоплювати довготривалі залежності та контекст.

У практиці науковця з даних частина роботи полягає в доборі правильного представлення залежно від задачі: для швидкої класифікації рецензій часто достатньо TF–IDF і лінійних моделей; для складних завдань семантичного пошуку або витягнення сутностей корисні вбудовування слів і контекстні мовні моделі. Важливо пам'ятати, що підготовка даних і прості методи іноді дають вражаючі результати, і вибір інструменту має базуватись на доступності даних, вимогах до точності й обмеженнях по часу на навчання.

## 7. Теорія ймовірності




## 8. Вступ в машинне навчання

Машинне навчання є на даний момент швидкозростаючою галуззю, хоча слід підкреслити, що початкові методи, які ми тут розглянемо (а саме лінійна регресія), з'явилися приблизно на 200 років раніше, ніж сам термін (зазвичай вважається, що Гаусс розробив метод найменших квадратів близько 1800 року). Більшість пізніших методів, про які ми говоримо, були добре вивчені в статистиці на початку століття. Ми все одно будемо використовувати загальний термін «машинне навчання» для позначення всіх цих методів, але важливо знати, що ці ідеї не виникли в спільноті ML. Натомість тема машинного навчання розвинулася завдяки поєднанню трьох різних елементів: 
- зростання обчислювальної потужності (машинне навчання, яке виникло з інформатики, завжди було фундаментально пов'язане з обчислювальними алгоритмами), 
- величезні обсяги доступних даних (сировина для методів машинного навчання) та 
- деякі значні досягнення в галузі алгоритмів, що відбулися за останні 30 років. Однак для початку ми розглянемо деякі з найпростіших алгоритмів, щоб систематизувати основні принципи.

```
import matplotlib.pyplot as plt
import pandas as pd

def build_plot(file_path, x, y):
    data = pd.read_csv(file_path)
    plt.scatter(data[x], data[y], c='blue', marker='x')
    plt.xlabel(x)
    plt.ylabel(y)
    return plt

build_plot('../resources/Salary_dataset.csv', 'YearsExperience', 'Salary').show()
```

![ml](./img/201-1.png)

Ось простий приклад залежності між стажем роботи співробітників та їхньою заробітною платою. Чи можна передбачити другий параметр на основі значення першого?

Можна припустити, що залежність відповідає лінійній моделі.

$$y \approx \theta_1 \cdot x + \theta_2$$


Тут 
- $\theta_1$ це “нахил” лінії, та (slope)
- $\theta_2$ зсув. (intercept)

Чи можемо ми робити передбачення даних?

```
import numpy as np

def build_line(plt, theta):
    xlim, ylim =(plt.gca().get_xlim(), plt.gca().get_ylim())
    plt.plot(xlim, [theta[0]*xlim[0]+theta[1], theta[0]*xlim[1]+theta[1]], 'C1')
    plt.xlim(xlim)
    plt.ylim(ylim)

plt = build_plot('../resources/Salary_dataset.csv', 'YearsExperience', 'Salary')
build_line(plt, np.array([9000, 25000]))
```

![ml](./img/201-2.png)


**Градієнтний спуск (Gradient descent)**

Припустимо, що прогнозована зарплата відповідає лінійній моделі. Як ми можемо знайти $\theta$? 

Є багато можливостей, але природною метою є мінімізація деякої різниці між цією лінією 
і спостережуваними даними, наприклад, квадратична втрата:

$$E(\theta) = \sum_{i}(\theta_{1}*x_{i} + \theta_{2} - y_{i} )^{2}$$

Тепер нам потрібно знайти параметри, які мінімізують значення функції $E(\theta)$.

$$minimize_{\theta}E(\theta)$$

Найкращий спосіб знайти значення функції $f(x)$, в якій її значення є мінімальним, — це знайти похідну. Ми починаємо з деякої точки і змінюємо вхідне значення в напрямку від'ємної похідної. У випадку багатозмінних функцій ми будемо використовувати часткові похідні.

Щоб знайти хороше значення $\theta$, ми можемо повторно робити кроки в напрямку 
негативних похідних для кожного значення. Ітерація:

$$ \theta_{1} := \theta_{1} - \alpha \frac{\delta}{\delta \theta_{1}} E(\theta_{1}, \theta_{2})$$

$$ \theta_{2} := \theta_{2} - \alpha \frac{\delta}{\delta \theta_{2}} E(\theta_{1}, \theta_{2})$$

де $\alpha$ — це невелике додатне число, яке називається розміром кроку.
Це алгоритм градієнтного спуску, основа сучасного машинного навчання.

Тут: 

$$\frac{\delta}{\delta \theta_{1}} E(\theta_{1}, \theta_{2}) = \sum_{i}2(\theta_{1}*x_{i} + \theta_{2} - y_{i} )*x_{i}$$

$$\frac{\delta}{\delta \theta_{2}} E(\theta_{1}, \theta_{2}) = \sum_{i}2(\theta_{1}*x_{i} + \theta_{2} - y_{i} )$$

Застосуємо градієнтний спуск до наших даних, спочатку нормалізувавши їх.

```
def build_plot_normalized(file_path, x, y):
    data = pd.read_csv(file_path)
    x_nor = (data[x] - min(data[x])) / (max(data[x]) - min(data[x]))
    y_nor = (data[y] - min(data[y])) / (max(data[y]) - min(data[y]))
    plt.scatter(x_nor, y_nor, c='blue', marker='x')
    plt.xlabel(x)
    plt.ylabel(y)
    return plt

build_plot_normalized('../resources/Salary_dataset.csv', 'YearsExperience', 'Salary').show()
```

Візуалізуємо процес градієнтного спуску.

```
# Visualizing the gradient descent:

def get_data(file_path, x_name, y_name):
    data = pd.read_csv(file_path)
    return data[x_name], data[y_name]

def normalize_data(x):
    x_normalized = (x - min(x)) / (max(x) - min(x))
    return x_normalized

def gradient_descent(x, y, iters, alpha):
    theta = np.array([0., 0.])
    for t in range(iters):
        theta[0] -= alpha/len(x) * 2 * sum((theta[0] * x + theta[1] - y)*x)
        theta[1] -= alpha/len(x) * 2 * sum((theta[0] * x + theta[1] - y) )
    return theta 

def plot_fit(x, y, theta):
    plt.scatter(x, y, marker = 'x')
    xlim, ylim =(plt.gca().get_xlim(), plt.gca().get_ylim())
    plt.plot(xlim, [theta[0]*xlim[0]+theta[1], theta[0]*xlim[1]+theta[1]], 'C1')
    plt.xlim(xlim)
    plt.ylim(ylim) 

x, y = get_data('../resources/Salary_dataset.csv', 'YearsExperience', 'Salary')
x_normalized, y_normalized = normalize_data(x), normalize_data(y)

#first iteration
theta = gradient_descent(x_normalized, y_normalized, 0, 0.1)
plot_fit(x_normalized, y_normalized, theta)
```

![ml](./img/201-3.png)

Як бачимо, на першій ітерації значення параметрів $\theta$ віддалені від бажаних. Проведемо іще декілька ітерацій.


```
theta = gradient_descent(x_normalized, y_normalized, 2, 0.1) # change third parameter to see more iterations
plot_fit(x_normalized, y_normalized, theta)
```

![ml](./img/201-4.png)

```
theta = gradient_descent(x_normalized, y_normalized, 5, 0.1) # change third parameter to see more iterations
plot_fit(x_normalized, y_normalized, theta)
```

![ml](./img/201-5.png)

```
theta = gradient_descent(x_normalized, y_normalized, 10, 0.1) # change third parameter to see more iterations
plot_fit(x_normalized, y_normalized, theta)
```

![ml](./img/201-6.png)

```
theta = gradient_descent(x_normalized, y_normalized, 100, 0.1) # change third parameter to see more iterations
plot_fit(x_normalized, y_normalized, theta)
```

![ml](./img/201-7.png)

Після 100-ї ітерації ми наблизилися із нашою лінійною моделлю до реального розподілу. 

Ми також можемо візуалізувати процес зміни параметрів $\theta$:

```
# Visualizing parameter updates

def gradient_descent_params(x, y, iters):
    thetas = []
    theta = np.array([0., 0.])
    alpha = 1.0
    for t in range(iters):
        thetas.append(theta.copy())
        theta[0] -= alpha/len(x) * 2 * sum((theta[0] * x + theta[1] - y)*x)
        theta[1] -= alpha/len(x) * 2 * sum((theta[0] * x + theta[1] - y) )
    return np.array(thetas)

def err(x, y, theta):
    return np.mean((np.outer(x, theta[:,0]) + theta[:,1] - y[:,None])**2,axis=0)

thetas = gradient_descent_params(x_normalized, y_normalized, 100)
plt.plot(thetas[:,0], thetas[:,1])
xlim, ylim =(np.array(plt.gca().get_xlim()), np.array(plt.gca().get_ylim()))
xlim += np.array([0,0.5])
ylim += np.array([-0.1, 0.1])

XX,YY = np.meshgrid(np.linspace(xlim[0],xlim[1],200), np.linspace(ylim[0], ylim[1],200))
ZZ = err(x_normalized.to_numpy(), y_normalized.to_numpy(), np.hstack([np.ravel(XX)[:,None], np.ravel(YY)[:,None]])).reshape(XX.shape)
#V = np.logspace(np.log(np.min(ZZ)), np.log(np.max(ZZ)), 30)
V = np.linspace(np.sqrt(np.min(ZZ)), np.sqrt(np.max(ZZ)), 25)**2
plt.clf()
plt.contour(XX,YY,ZZ, V, colors=('C0',))
plt.plot(thetas[:,0], thetas[:,1], 'C1-x')
plt.xlabel("theta1")
plt.ylabel("theta2")
```

![ml](./img/201-8.png)

Зробимо те саме лінійне прогнозування, використовуючи бібліотеку scikit-learn:

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression

data = pd.read_csv('../resources/Salary_dataset.csv')
X = np.array(data['YearsExperience']).reshape(-1, 1)  
y = np.array(data['Salary'])                

# етап тренування моделі
model = LinearRegression()
model.fit(X, y)

# отримуємо параметри theta
slope = model.coef_[0]       # theta_1
intercept = model.intercept_ # theta_2 
print("slope:", slope, "intercept:", intercept)

# будуємо лінію 
X_line = np.linspace(X.min(), X.max(), 100).reshape(-1, 1)
y_line = model.predict(X_line)

# plot 
plt.scatter(X, y, c='blue', marker='x')          # початкові дані
plt.plot(X_line, y_line)   # лінія
plt.xlabel("X")
plt.ylabel("y")
plt.title("Linear Regression Fit")
plt.show()
```

![ml](./img/201-9.png)


**Трохи більше про машинне навчання.**

Машинне навчання надає можливість автоматично встановлювати прогнозну модель на основі даних.

*Термінологія*. Функція втрат: $l: y × y \rightarrow \mathbb{R}$, вимірює різницю між прогнозом і 
фактичним результатом $$l(y',y)=(y'-y)^{2}$$

Канонічна задача оптимізації машинного навчання:

$$minimize_{\theta}\sum_{i}l(h_{\theta}(x^{i}), y^{i})$$

Практично кожен алгоритм машинного навчання має таку форму, просто вкажіть
- Що таке функція гіпотези?
- Що таке функція втрат?
- Як вирішити задачу оптимізації?

*Приклади алгоритмів машинного навчання*

- Найменші квадрати: лінійна гіпотеза, квадратична втрата, (зазвичай) аналітичне 
рішення.
- Лінійна регресія: лінійна гіпотеза.
- Машина опорних векторів (support vector machine): {лінійна або ядрова гіпотеза, втрата шарніра, *}
- Нейронна мережа: {складена нелінійна функція, *, (зазвичай) градієнтний 
спуск)
- Дерево рішень (decision trees): {ієрархічні півплощини, вирівняні по осі, *, жадібна оптимізація}
- Наївний Байєс: {лінійна гіпотеза, спільна ймовірність за певних 
припущень незалежності, аналітичне рішення}





## 9. Лінійна Класифікація



```

```

```

```

## 10. Нелінійні Алгоритми Машинного Навчання


```

```

```

```

## 11. Ансамблеве навчання

## 12. Навчання без учителя (Unsupervised Learning)

## 13. Перевірка статистичних гіпотез (Hypothesis testing)

## 14. Рекомендаційні системи (Recommender Systems)

## 15. Часові Ряди

## 16. Моделювання Кризових Явищ

## 17. Використання нейронних мереж для аналізу даних

