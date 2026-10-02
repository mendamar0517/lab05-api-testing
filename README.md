# Лабораторийн ажил №5 — Postman / Newman API Testing

**Оюутны нэр:** О.Мэнд-Амар  
**Оюутны код:** B232270001  
**Хичээл:** F.CSA313 — Программ хангамжийн чанарын баталгаа ба туршилт

## Орчны хувилбар

**Node.js**

`node -v` → `v20.20.0`

**Newman**

`newman -v` → `6.2.2`

---

## 1. Лабораторийн ажлын зорилго

Энэхүү лабораторийн ажлын зорилго нь REST API-г Postman ашиглан тестлэх, Newman ашиглан command line орчноос автомат тест ажиллуулах, зөв болон зориуд буруу oracle ашигласан тестийн үр дүнг шалгахад оршино.

Лабораторийн хүрээнд API-ийн эерэг, сөрөг болон алдааны нөхцөлүүдийг тодорхойлж, тус бүрт тохирох status code, response value болон assertion-уудыг боловсруулсан.

Мөн тестийн бие даасан байдлыг хангахын тулд шаардлагатай student болон course-ийн өгөгдлийг тухайн тестийн өмнө PUT request ашиглан setup хийсэн.

---

## 2. Ашигласан технологи

- Node.js `v20.20.0`
- Newman `6.2.2`
- Postman
- JavaScript
- REST API
- Git
- GitHub

API серверийг Node.js ашиглан локал орчинд ажиллуулсан.

Base URL:

`http://localhost:3000`

---

## 3. API-ийн бүтэц

Тестэлсэн API нь оюутан, хичээл болон бүртгэлийн мэдээлэлтэй ажиллана.

### 3.1 Student setup

**Endpoint:** `PUT /students/:id`

Жишээ request body:

`{"status":"active","coursesTaken":["CS201"]}`

Амжилттай үед:

`200 {"result":"OK"}`

### 3.2 Course setup

**Endpoint:** `PUT /courses/:id`

Жишээ request body:

`{"prerequisites":["CS201"]}`

Амжилттай үед:

`200 {"result":"OK"}`

### 3.3 Course харах

**Endpoint:** `GET /courses/:id`

Course байгаа үед `200` status буцаана.

Course байхгүй үед `404` status болон `ERROR_NO_COURSE` буцаана.

### 3.4 Registration үүсгэх

**Endpoint:** `POST /registrations`

Жишээ request body:

`{"studentID":"B231234567","courseID":"CS313"}`

Амжилттай үед:

`201 {"result":"OK","registrationID":N}`

`registrationID` нь серверийн memory-д хадгалагдаж буй бүртгэлийн дарааллаас хамаарч өөрчлөгдөх боломжтой. Иймээс яг тодорхой утгыг шалгаагүй бөгөөд зөвхөн:

- `registrationID` талбар байгаа эсэх
- `registrationID` нь `number` төрөлтэй эсэх

гэсэн oracle ашигласан.

---

## 4. Тест дизайны сонголт ба төлөөлөх утгууд

Лабораторийн зааврын дагуу `POST /registrations` функцийн үндсэн сонголтуудыг дараах байдлаар тодорхойлсон.

| Сонголт             | Төлөөлөх утга          |
| ------------------- | ---------------------- |
| Student-ийн төлөв   | Active                 |
| Student-ийн төлөв   | Inactive               |
| Student             | Байгаа                 |
| Student             | Байхгүй                |
| coursesTaken        | Prerequisite хангасан  |
| coursesTaken        | Prerequisite хангаагүй |
| Course              | Байгаа                 |
| Course              | Байхгүй                |
| Course prerequisite | Prerequisite байгаа    |
| Course prerequisite | Prerequisite байхгүй   |
| Request field       | `studentID` байгаа     |
| Request field       | `studentID` байхгүй    |
| Request field       | `courseID` байгаа      |
| Request field       | `courseID` байхгүй     |
| JSON format         | Зөв JSON               |
| JSON format         | Malformed JSON         |

---

## 5. Тестийн спецификаци

Нийт 10 үндсэн specification боловсруулсан.

| Test Case | Specification                                 | Expected Status | Expected Result          |
| --------- | --------------------------------------------- | --------------: | ------------------------ |
| TC01      | Active student + prerequisite хангасан course |             201 | `OK`                     |
| TC02      | Student байхгүй                               |             200 | `ERROR_NO_STUDENT`       |
| TC03      | Student inactive                              |             200 | `ERROR_INACTIVE_STUDENT` |
| TC04      | Course байхгүй                                |             200 | `ERROR_NO_COURSE`        |
| TC05      | Prerequisite дутуу                            |             200 | `ERROR_PREREQUISITES`    |
| TC06      | Бүх prerequisite хангасан                     |             201 | `OK`                     |
| TC07      | Student болон course хоёулаа байхгүй          |             200 | `ERROR_NO_STUDENT`       |
| TC08      | `studentID` байхгүй                           |             400 | `ERROR_BAD_REQUEST`      |
| TC09      | `courseID` байхгүй                            |             400 | `ERROR_BAD_REQUEST`      |
| TC10      | Malformed JSON                                |             400 | `ERROR_BAD_JSON`         |

Ингэснээр шаардлагатай 8+ specification-ийн нөхцөл хангагдсан.

---

## 6. Тестийн бие даасан байдал

Тест бүрийг аль болох бие даасан байдлаар зохион байгуулсан.

Серверийн өгөгдөл in-memory хэлбэрээр хадгалагддаг тул тухайн тестэд шаардлагатай student болон course-ийн өгөгдлийг өөрийн setup PUT request-ээр үүсгэж эсвэл шинэчилдэг.

Missing student болон missing course төрлийн тестүүдэд тухайн entity-г зориудаар setup хийгээгүй бөгөөд бусад шаардлагатай entity-г setup хийсэн.

Жишээлбэл:

- TC02-д course-ийг setup хийж, student-ийг зориудаар байхгүй болгосон.
- TC04-д student-ийг setup хийж, course-ийг зориудаар байхгүй болгосон.
- TC07-д student болон course хоёуланг нь setup хийхгүйгээр хоёр entity хоёулаа байхгүй нөхцөлийг шалгасан.

Ингэснээр тестүүд өмнөх тестийн санах ойд үлдсэн өгөгдлөөс хамаарахгүйгээр ажиллах боломжтой.

---

## 7. Тестийн oracle

Тестүүдэд дараах oracle-уудыг ашигласан:

- HTTP status code шалгах
- Response-ийн `result` утгыг шалгах
- `registrationID` property байгаа эсэхийг шалгах
- `registrationID` нь `number` төрөлтэй эсэхийг шалгах
- `missing` массивт шаардлагатай prerequisite байгаа эсэхийг шалгах

`registrationID`-ийн яг утгыг шалгаагүй. Учир нь серверийг дахин ажиллуулах болон collection-ийг дахин ажиллуулах үед registration ID өөрчлөгдөж болно.

---

## 8. TC01 — Successful Registration

Active student болон prerequisite хангасан course ашиглан амжилттай бүртгэл үүсгэсэн.

### Student setup

`PUT /students/B231234567`

Request body:

`{"status":"active","coursesTaken":["CS201"]}`

### Course setup

`PUT /courses/CS313`

Request body:

`{"prerequisites":["CS201"]}`

### Registration

`POST /registrations`

Request body:

`{"studentID":"B231234567","courseID":"CS313"}`

### Oracle

- HTTP status `201`
- `result = OK`
- `registrationID` property байгаа
- `registrationID` нь `number` төрөлтэй

---

## 9. TC02 — Student Not Found

Бүртгэлд байхгүй student ашигласан.

Course setup хийж, student-ийг зориудаар байхгүй болгосон.

Request body:

`{"studentID":"B231234569","courseID":"CS313"}`

### Expected result

- HTTP status `200`
- `result = ERROR_NO_STUDENT`

---

## 10. TC03 — Inactive Student

`inactive` төлөвтэй student ашигласан.

Student setup body:

`{"status":"inactive","coursesTaken":[]}`

Course setup body:

`{"prerequisites":[]}`

### Expected result

- HTTP status `200`
- `result = ERROR_INACTIVE_STUDENT`

---

## 11. TC04 — Course Not Found

Байхгүй course ашигласан.

Student setup хийж, course-ийг зориудаар байхгүй болгосон.

Request body:

`{"studentID":"B231234571","courseID":"CS999"}`

### Expected result

- HTTP status `200`
- `result = ERROR_NO_COURSE`

---

## 12. TC05 — Missing Prerequisite

Student prerequisite хичээлийг аваагүй нөхцөлөөр шалгасан.

### Student setup

`{"status":"active","coursesTaken":[]}`

### Course setup

`{"prerequisites":["CS201"]}`

### Expected result

- HTTP status `200`
- `result = ERROR_PREREQUISITES`
- `missing` массивт `CS201` байна

---

## 13. TC06 — Prerequisites Satisfied

Student бүх prerequisite хичээлийг авсан нөхцөлөөр шалгасан.

### Student setup

`{"status":"active","coursesTaken":["CS201"]}`

### Course setup

`{"prerequisites":["CS201"]}`

### Expected result

- HTTP status `201`
- `result = OK`
- `registrationID` property байгаа
- `registrationID` нь `number` төрөлтэй

---

## 14. TC07 — Double Error Priority

Student болон course хоёулаа байхгүй нөхцөлийг шалгасан.

Request body:

`{"studentID":"B231234574","courseID":"CS998"}`

Зааварт олон алдаа зэрэг үүсэх үед аль алдааг буцаах нь урьдчилан тодорхойлогдоогүй байсан тул бодит API дээр туршиж үзсэн.

Туршилтаар student-ийн алдаа түрүүлж буцсан.

### Expected result

- HTTP status `200`
- `result = ERROR_NO_STUDENT`

---

## 15. TC08 — Missing StudentID

Request-д `studentID` талбар байхгүй.

Course setup:

`{"prerequisites":[]}`

Request body:

`{"courseID":"CS313"}`

### Expected result

- HTTP status `400`
- `result = ERROR_BAD_REQUEST`

---

## 16. TC09 — Missing CourseID

Request-д `courseID` талбар байхгүй.

Student setup:

`{"status":"active","coursesTaken":[]}`

Request body:

`{"studentID":"B231234575"}`

### Expected result

- HTTP status `400`
- `result = ERROR_BAD_REQUEST`

---

## 17. TC10 — Malformed JSON

JSON-ийн төгсгөлийн `}`-г зориудаар орхиж malformed JSON үүсгэсэн.

Request body:

`{"studentID":"B231234576","courseID":"CS313"`

### Expected result

- HTTP status `400`
- `result = ERROR_BAD_JSON`

---

## 18. Postman Collection

Бүх тестүүдийг `Lab05 - Registration API Tests` collection-д зохион байгуулсан.

Collection дотор дараах 10 үндсэн test case байна:

1. TC01 - Successful Registration
2. TC02 - Student Not Found
3. TC03 - Inactive Student
4. TC04 - Course Not Found
5. TC05 - Missing Prerequisite
6. TC06 - Prerequisites Satisfied
7. TC07 - Double Error Priority
8. TC08 - Missing StudentID
9. TC09 - Missing CourseID
10. TC10 - Malformed JSON

Collection нь нийт:

**23 requests**

**49 assertions**

ажиллуулдаг.

---

## 19. Newman PASS тест

Зөв oracle бүхий collection-ийг Newman ашиглан ажиллуулсан.

Ашигласан command:

`newman run lab05-collection.json 2>&1 | tee results/newman-pass.txt`

Exit code шалгахдаа zsh орчинд:

`echo "exit=$pipestatus[1]"`

ашигласан.

### PASS үр дүн

| Үзүүлэлт           | Executed | Failed |
| ------------------ | -------: | -----: |
| Iterations         |        1 |      0 |
| Requests           |       23 |      0 |
| Test scripts       |       46 |      0 |
| Prerequest scripts |       23 |      0 |
| Assertions         |       49 |      0 |

**Exit code: `0`**

Ингэснээр зөв oracle бүхий collection-ийн 49 assertion бүгд амжилттай ажилласан.

README-д дурдсан нийт тестийн тоо нь `newman-pass.txt` файлын **assertions executed = 49** тоотой таарна.

---

## 20. Wrong Oracle тест

Зориуд буруу oracle ашигласан хувилбарыг тусдаа collection болгон хадгалсан.

Файл:

`lab05-collection-fail.json`

TC01-ийн HTTP status assertion-ийг зориудаар буруу болгож, бодит `201` response-ийг `200` гэж хүлээсэн.

Бодит API response:

`201`

Буруу oracle:

`200`

### FAIL үр дүн

| Үзүүлэлт   | Executed | Failed |
| ---------- | -------: | -----: |
| Requests   |       23 |      0 |
| Assertions |       49 |      1 |

**Exit code: `1`**

Failed assertion:

`Registration status is 201`

Expected:

`200`

Actual:

`201`

Энэ нь зориуд буруу oracle ашигласан үед Newman тухайн assertion-ийн алдааг зөв илрүүлж, exit code `1` буцааж байгааг харуулсан.

Үндсэн зөв collection болох `lab05-collection.json`-ийг өөрчлөлгүй хадгалсан.

---

## 21. Server Down тест

API серверийг `Ctrl+C` ашиглан зогсоосны дараа зөв collection-ийг Newman ашиглан ажиллуулсан.

Ашигласан command:

`newman run lab05-collection.json 2>&1 | tee results/newman-down.txt`

Сервер ажиллахгүй байсан тул дараах connection error гарсан:

`ECONNREFUSED 127.0.0.1:3000`

### DOWN үр дүн

- Requests: `23`
- Connection error: `ECONNREFUSED`
- Exit code: `1`

Энэ нь oracle-ийн functional failure биш, харин Newman API сервертэй холбогдож чадаагүй interface/connection error юм.

Иймээс `newman-down.txt` дахь `ECONNREFUSED` нь сервер унтарсан нөхцөлийг баталгаажуулна.

---

## 22. Newman үр дүнгийн файлууд

Newman-ийн гурван өөр нөхцөлийн бүтэн текст output-ийг `results/` хавтсанд хадгалсан.

### PASS

[results/newman-pass.txt](./results/newman-pass.txt)

- 49 assertions
- 0 failed
- exit code `0`

### FAIL

[results/newman-fail.txt](./results/newman-fail.txt)

- 49 assertions
- 1 failed
- exit code `1`

### SERVER DOWN

[results/newman-down.txt](./results/newman-down.txt)

- `ECONNREFUSED 127.0.0.1:3000`
- exit code `1`

Багшийн зааврын дагуу эдгээр нь Newman-ийн бүтэн текст output хэлбэрээр repository-д хадгалагдсан.

---

## 23. Төслийн файлууд

Доорх файлуудыг GitHub repository дээрээс шууд нээж болно.

### Үндсэн файлууд

- [server.js](./server.js)
- [lab05-collection.json](./lab05-collection.json)
- [lab05-collection-fail.json](./lab05-collection-fail.json)
- [README.md](./README.md)

### Newman үр дүн

- [newman-pass.txt](./results/newman-pass.txt)
- [newman-fail.txt](./results/newman-fail.txt)
- [newman-down.txt](./results/newman-down.txt)

### GitHub repository

[lab05-api-testing — GitHub](https://github.com/mendamar0517/lab05-api-testing)

---

## 24. Төслийн бүтэц

```text
lab05-api-testing/
├── .gitignore
├── README.md
├── server.js
├── lab05-collection.json
├── lab05-collection-fail.json
└── results/
    ├── newman-pass.txt
    ├── newman-fail.txt
    └── newman-down.txt
```

## 25. AI ашиглалт

Лабораторийн ажлын явцад AI хэрэгслийг дараах зорилгоор туслах хэрэгсэл болгон ашигласан:

- Postman test script-ийн syntax шалгах
- Newman command болон exit code шалгах
- Test case-ийн бүтэц боловсруулах
- Test specification боловсруулах
- Assertion болон oracle боловсруулах
- Алдаа оношлох
- README-ийн бүтэц боловсруулах

Гэхдээ API-ийн бодит response, Postman-ийн test result болон Newman-ийн output-ийг өөрийн локал орчинд ажиллуулж шалгасан.

Тухайлбал:

- PASS collection → 49 assertions, 0 failures
- FAIL collection → 49 assertions, 1 intentional failure
- Server DOWN → ECONNREFUSED

гэсэн үр дүнг бодитоор ажиллуулж баталгаажуулсан.

---

## 26. Дүгнэлт

Энэхүү лабораторийн ажлаар REST API-г Postman ашиглан системтэйгээр тестлэх, Newman ашиглан command line орчноос автомат тест ажиллуулах үйл явцыг хэрэгжүүлсэн. Тест дизайны хүрээнд сонголтуудыг тодорхойлж, тэдгээрт тохирох төлөөлөх утгууд болон 10 үндсэн specification боловсруулсан. Хамгийн их анхаарал шаардсан хэсэг нь student, course болон prerequisite-ийн төлөвүүдийг хооронд нь зөв хослуулж, бие даасан test case болгох явдал байсан. Мөн student болон course хоёулаа байхгүй зэрэг давхар алдааны нөхцөлийг бодитоор ажиллуулж, API-ийн буцаасан error priority-г тодорхойлсон. Нийт 10 үндсэн test case бүхий collection нь 23 request, 49 assertion ажиллуулдаг бөгөөд зөв oracle бүхий хувилбар бүх 49 assertion-ийг амжилттай давсан. Зориудаар буруу oracle ашигласан хувилбар нь 49 assertion-оос 1 дээр алдаж, exit code 1 буцаасан. Серверийг унтраасан үед ECONNREFUSED connection error гарч, энэ нь functional oracle-ийн алдаанаас ялгаатай болохыг шалгасан. Ийнхүү Postman collection, зөв болон буруу oracle-ийн үр дүн, server-down нөхцөл болон Newman-ийн автомат тестийн процессыг бүрэн хэрэгжүүлсэн.
