cat > README.md <<'EOF'

# Лабораторийн ажил №5 — Postman / Newman API Testing

**Оюутан:** О.Мэнд-Амар  
**Оюутны код:** B232270001  
**Хичээл:** F.CSA313 — Программ хангамжийн чанарын баталгаа ба туршилт

---

## 1. Лабораторийн ажлын зорилго

Энэхүү лабораторийн ажлын зорилго нь REST API-г Postman ашиглан тестлэх, Newman ашиглан command line орчноос автомат тест ажиллуулах, мөн зөв болон буруу oracle ашигласан тестийн үр дүнг шалгахад оршино.

Мөн API-ийн эерэг, сөрөг болон алдааны нөхцөлүүдийг тодорхойлж, тус бүрт тохирох assertion болон oracle боловсруулсан.

---

## 2. Ашигласан технологи

- Node.js v20.20.0
- Newman v6.2.2
- Postman
- JavaScript
- REST API
- Git / GitHub

API серверийг Node.js ашиглан локал орчинд `http://localhost:3000` хаягаар ажиллуулсан.

---

## 3. API-ийн бүтэц

Тестэлсэн API нь оюутан, хичээл болон бүртгэлийн мэдээлэлтэй ажиллана.

### 3.1 Оюутан тохируулах

**Endpoint:** `PUT /students/:id`

Жишээ request body: `{"status":"active","coursesTaken":["CS201"]}`

Амжилттай үед response: `{"result":"OK"}`

### 3.2 Хичээл тохируулах

**Endpoint:** `PUT /courses/:id`

Жишээ request body: `{"prerequisites":["CS201"]}`

Амжилттай үед response: `{"result":"OK"}`

### 3.3 Хичээлд бүртгүүлэх

**Endpoint:** `POST /registrations`

Жишээ request body: `{"studentID":"B231234567","courseID":"CS313"}`

Амжилттай үед response нь `{"result":"OK","registrationID":1}` хэлбэртэй байна.

`registrationID` нь серверийн memory-д хадгалагдаж буй бүртгэлийн дарааллаас хамаарч өөрчлөгдөх боломжтой тул яг тодорхой утгыг шалгаагүй. Харин `registrationID` талбар байгаа эсэх болон `number` төрөлтэй эсэхийг шалгасан.

---

## 4. Тестийн стратеги

Тест бүрийг бие даасан байдлаар зохион байгуулсан.

Серверийн өгөгдөл memory-д хадгалагддаг тул шаардлагатай student болон course-ийн өгөгдлийг тухайн тестийн өмнө `PUT` request ашиглан өөрөө тохируулсан.

Тестүүдэд дараах шалгалтуудыг ашигласан:

- HTTP status code шалгах
- Response-ийн `result` утгыг шалгах
- `registrationID` байгаа эсэхийг шалгах
- `registrationID` нь number төрөлтэй эсэхийг шалгах
- `missing` массивыг шалгах
- Сөрөг нөхцөлүүдийг шалгах
- Олон алдаа зэрэг үүсэх үеийн priority-г шалгах

---

## 5. Тестийн тохиолдлууд

Нийт 10 үндсэн test case боловсруулсан.

| Test Case | Тестийн нөхцөл                                | Хүлээгдэж буй үр дүн                 |
| --------- | --------------------------------------------- | ------------------------------------ |
| TC01      | Active student + prerequisite хангасан course | `201`, `result=OK`, `registrationID` |
| TC02      | Student байхгүй                               | `200`, `ERROR_NO_STUDENT`            |
| TC03      | Student inactive                              | `200`, `ERROR_INACTIVE_STUDENT`      |
| TC04      | Course байхгүй                                | `200`, `ERROR_NO_COURSE`             |
| TC05      | Prerequisite дутуу                            | `200`, `ERROR_PREREQUISITES`         |
| TC06      | Бүх prerequisite хангасан                     | `201`, `result=OK`                   |
| TC07      | Student болон course хоёулаа байхгүй          | `200`, `ERROR_NO_STUDENT`            |
| TC08      | `studentID` байхгүй                           | `400`, `ERROR_BAD_REQUEST`           |
| TC09      | `courseID` байхгүй                            | `400`, `ERROR_BAD_REQUEST`           |
| TC10      | Malformed JSON                                | `400`, `ERROR_BAD_JSON`              |

---

## 6. TC01 — Successful Registration

Active student болон prerequisite хангасан course ашиглан амжилттай бүртгэл үүсгэсэн.

Student setup: `PUT /students/B231234567`

Request body: `{"status":"active","coursesTaken":["CS201"]}`

Course setup: `PUT /courses/CS313`

Request body: `{"prerequisites":["CS201"]}`

Registration request: `POST /registrations`

Request body: `{"studentID":"B231234567","courseID":"CS313"}`

Шалгасан нөхцөлүүд:

- HTTP status `201`
- `result` нь `OK`
- `registrationID` талбар байгаа
- `registrationID` нь number төрөлтэй

---

## 7. TC02 — Student Not Found

Бүртгэлд байхгүй student ашигласан.

Request body: `{"studentID":"B231234569","courseID":"CS313"}`

Хүлээгдэж буй:

- HTTP status `200`
- `result = ERROR_NO_STUDENT`

---

## 8. TC03 — Inactive Student

`inactive` төлөвтэй student ашигласан.

Student setup body: `{"status":"inactive","coursesTaken":[]}`

Хүлээгдэж буй:

- HTTP status `200`
- `result = ERROR_INACTIVE_STUDENT`

---

## 9. TC04 — Course Not Found

Байхгүй course ашигласан.

Request body: `{"studentID":"B231234571","courseID":"CS999"}`

Хүлээгдэж буй:

- HTTP status `200`
- `result = ERROR_NO_COURSE`

---

## 10. TC05 — Missing Prerequisite

Student prerequisite хичээлийг аваагүй нөхцөлөөр шалгасан.

Student setup body: `{"status":"active","coursesTaken":[]}`

Course setup body: `{"prerequisites":["CS201"]}`

Хүлээгдэж буй:

- HTTP status `200`
- `result = ERROR_PREREQUISITES`
- `missing` массивт `CS201` байна

---

## 11. TC06 — Prerequisites Satisfied

Student бүх prerequisite хичээлийг авсан нөхцөлөөр шалгасан.

Student setup body: `{"status":"active","coursesTaken":["CS201"]}`

Course setup body: `{"prerequisites":["CS201"]}`

Хүлээгдэж буй:

- HTTP status `201`
- `result = OK`
- `registrationID` талбар байгаа
- `registrationID` нь number төрөлтэй

---

## 12. TC07 — Double Error Priority

Student болон course хоёулаа байхгүй нөхцөлөөр шалгасан.

Request body: `{"studentID":"B231234574","courseID":"CS998"}`

Туршилтаар API нь student-ийн алдааг түрүүлж буцаасан.

Хүлээгдэж буй:

- HTTP status `200`
- `result = ERROR_NO_STUDENT`

---

## 13. TC08 — Missing StudentID

Request-д `studentID` талбар байхгүй.

Request body: `{"courseID":"CS313"}`

Хүлээгдэж буй:

- HTTP status `400`
- `result = ERROR_BAD_REQUEST`

---

## 14. TC09 — Missing CourseID

Request-д `courseID` талбар байхгүй.

Request body: `{"studentID":"B231234575"}`

Хүлээгдэж буй:

- HTTP status `400`
- `result = ERROR_BAD_REQUEST`

---

## 15. TC10 — Malformed JSON

JSON-ийн төгсгөлийн `}`-г зориудаар орхиж malformed JSON үүсгэсэн.

Хүлээгдэж буй:

- HTTP status `400`
- `result = ERROR_BAD_JSON`

---

## 16. Postman Collection

Бүх тестүүдийг `Lab05 - Registration API Tests` collection-д зохион байгуулсан.

Collection дотор дараах 10 test case байна:

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

Нийт **49 assertions** ажиллуулсан.

---

## 17. Newman PASS тест

Зөв oracle бүхий collection-ийг Newman ашиглан ажиллуулсан.

Ашигласан command:

`newman run lab05-collection.json 2>&1 | tee results/newman-pass.txt`

Exit code шалгасан:

`echo "exit=$pipestatus[1]"`

Үр дүн:

| Үзүүлэлт           | Үр дүн |
| ------------------ | -----: |
| Iterations         |      1 |
| Requests           |     23 |
| Test scripts       |     46 |
| Prerequest scripts |     23 |
| Assertions         |     49 |
| Failed             |      0 |
| Exit code          |      0 |

Ингэснээр зөв oracle бүхий collection бүх тестээ амжилттай давсан.

---

## 18. Wrong Oracle тест

Буруу oracle ашигласан хувилбарыг тусдаа collection болгон хадгалсан.

Файл: `lab05-collection-fail.json`

TC01-ийн HTTP status assertion-ийг зориудаар буруу болгож, `201`-ийн оронд `200` гэж шалгасан.

Бодит response нь `201` байсан.

Newman-ийн үр дүн:

| Үзүүлэлт   | Үр дүн |
| ---------- | -----: |
| Requests   |     23 |
| Assertions |     49 |
| Failed     |      1 |
| Exit code  |      1 |

Алдаа: `Registration status is 201` assertion дээр expected `200` but got `201`.

Ингэснээр буруу oracle ашигласан үед Newman тестийг амжилтгүй гэж зөв илрүүлж байгааг баталгаажуулсан.

---

## 19. Server Down тест

API серверийг зогсоосны дараа зөв collection-ийг Newman ашиглан ажиллуулсан.

Ашигласан command:

`newman run lab05-collection.json 2>&1 | tee results/newman-down.txt`

Сервер ажиллахгүй байсан тул `ECONNREFUSED 127.0.0.1:3000` connection error гарсан.

Энэ нь API-ийн response oracle-ийн алдаа биш бөгөөд сервертэй холбогдож чадаагүй interface/connection error юм.

Үр дүнд:

- Requests: 23
- Connection error: `ECONNREFUSED`
- Exit code: 1

---

## 20. Үр дүнгийн файлууд

`results` хавтсанд Newman-ийн гурван өөр нөхцөлийн үр дүнг хадгалсан.

- `results/newman-pass.txt` — зөв collection-ийн үр дүн
- `results/newman-fail.txt` — буруу oracle-ийн үр дүн
- `results/newman-down.txt` — сервер унтарсан үеийн үр дүн

### PASS

`49 assertions`, `0 failures`, `exit=0`

### FAIL

`49 assertions`, `1 failure`, `exit=1`

### SERVER DOWN

`ECONNREFUSED`, `exit=1`

---

## 21. Төслийн бүтэц

Төслийн үндсэн бүтэц:

- `.gitignore`
- `README.md`
- `server.js`
- `lab05-collection.json`
- `lab05-collection-fail.json`
- `results/newman-pass.txt`
- `results/newman-fail.txt`
- `results/newman-down.txt`

Багшийн өгсөн `.docx` зааврын файлыг repository-д оруулаагүй.

---

## 22. Git ашиглалт

Төслийг тусдаа Git repository болгон зохион байгуулсан.

Лабораторийн ажлын бодит үе шаттай уялдуулан утга бүхий commit хийсэн.

Одоогийн commit-үүд:

- `Set up Lab 5 API project`
- `Add Postman API test collection`

Newman-ийн үр дүн болон documentation-ийг дараагийн milestone байдлаар commit хийнэ.

---

## 23. AI ашиглалт

Лабораторийн ажлын явцад AI хэрэгслийг дараах зорилгоор ашигласан:

- Postman test script-ийн syntax шалгах
- Newman command болон exit code шалгах
- Test case-ийн бүтэц боловсруулах
- README-ийн бүтэц боловсруулах
- Алдаа оношлох
- Assertion болон oracle боловсруулах

API-ийн бодит response, Postman болон Newman-ийн үр дүнг өөрийн орчинд ажиллуулж баталгаажуулсан.

---

## 24. Дүгнэлт

Энэхүү лабораторийн ажлаар REST API-г Postman ашиглан системтэйгээр тестэлж сурсан.

Нийт 10 үндсэн test case боловсруулж, эерэг, сөрөг болон алдааны нөхцөлүүдийг хамруулсан.

Тест бүр шаардлагатай setup үйлдлээ өөрөө хийдэг байдлаар зохион байгуулагдсан.

Newman ашиглан collection-ийг command line орчноос автомат ажиллуулж, нийт 49 assertion шалгасан.

Зөв oracle бүхий collection нь 49 assertion-оос 49-ийг амжилттай давж, exit code 0 гарсан.

Зориудаар буруу oracle ашигласан collection нь нэг assertion дээр алдаж, exit code 1 гарсан.

Серверийг унтраасан үед `ECONNREFUSED` connection error гарч байгааг тусад нь шалгасан.

Ингэснээр API-ийн functional error болон interface/connection error-ийн ялгааг практик дээр харсан.

Мөн Postman collection, Newman-ийн үр дүн болон documentation-ийг Git repository-д зохион байгуулж хадгалсан.

EOF
