**年度と授業選択セレクトボックス**

1. **`fetchYearSemesterOptions`**  
   - **リクエスト先:** `https://971cm.com/web/user/years`　(例えばね)
   - **戻り値の形式:**  
     - `item.year` → 年度（例：`2024`）  
     - `item.semester` → 学期（例：`前期`）  
   - **処理の説明:**  
     - 取得したデータを元に、「2024年度前期」の形式で表示するオプションを作成  
     - APIから戻ってきた`item`オブジェクトの`year`と`semester`を使って、`label`に表示する内容を生成し、`value`としてその組み合わせを設定  
   
    - **`/user/years`エンドポイント**は、どんなデータを返すか
        - 例:  
            ```json
            [
            { "year": 2024, "semester": "前期" },
            { "year": 2024, "semester": "後期" }
            ]
            ```

---

2. **`fetchLecturesForSemester`**  
   - **リクエスト先:** `https://971cm.com/web/lectures?year=${year}&semester=${semester}`　(例えばね)
   - **戻り値の形式:**  
     - `response.data` → 授業名のリスト（例：`['作品制作1B', '学力基礎養成講座1B']`）  
   - **処理の説明:**  
     - ユーザーが選択した年度と学期に対応する授業名リストを取得  
     - APIから戻ってきたデータを`lectures`ステートにセット


    - **`/lectures`エンドポイント**は、学期と年度に対応する授業リストを返すこと
        - 例:  
            ```json
            [
            "作品制作1B",
            "学力基礎養成講座1B"
            ]
            ```

---

## 学生アカウント管理（管理者）のリクエスト

### ✅すべての学生の一覧を取得する
```js
// 返してほしい値
[
  {
    student_id: "学籍番号",
    student_f_name: "学生の名前",
    student_l_name: "学生の苗字"
  }
]

// SQLで使う値
// 特になし

// リクエスト: GET
url = "/web/students/";
```

### ✅学生アカウントを追加
```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  student_id: "学籍番号",
  student_pass: "パスワード（初期パスワード）",
  student_f_name: "学生の名前",
  student_l_name: "学生の苗字",
  student_f_kana: "学生の名前（カナ）",
  student_l_kana: "学生の苗字（カナ）"
}

// リクエスト: POST
url = "/web/students/";
```

### ✅学生アカウントを編集
```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  student_id: "学籍番号",
  student_pass: "パスワード（初期パスワード）",
  student_f_name: "学生の名前",
  student_l_name: "学生の苗字",
  student_f_kana: "学生の名前（カナ）",
  student_l_kana: "学生の苗字（カナ）"
}

// リクエスト: PUT
url = "/web/students/";
```

### ✅学籍番号から特定の学生アカウントを取得
```js
// 返してほしい値
{
  student_id: "学籍番号",
  student_pass: "パスワード（初期パスワード）",
  student_f_name: "学生の名前",
  student_l_name: "学生の苗字",
  student_f_kana: "学生の名前（カナ）",
  student_l_kana: "学生の苗字（カナ）"
}

// SQLで使う値
// String型 student_id 学籍番号

// リクエスト: GET
url = "/web/students/{student_id}";
```

### ✅学生アカウントのパスワードを初期化する
```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}


// SQLで使う値
{
  student_id: "学籍番号",
  student_pass: "パスワード（初期パスワード）"
}

// リクエスト: GET
url = "/web/students/password/";
```

---

## 教員アカウント管理（管理者）のリクエスト

### ✅すべての教員の一覧を取得する
```js
// 返してほしい値
[
  {
    teacher_id: "教員ID",
    teacher_f_name: "教員の名前",
    teacher_l_name: "教員の苗字"
  }
]

// SQLで使う値
// 特になし

// リクエスト: GET
url = "/web/teachers/";
```

### ✅教員アカウントを追加
```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  teacher_id: "教員ID",
  teacher_pass: "パスワード（初期パスワード）",
  teacher_f_name: "教員の名前",
  teacher_l_name: "教員の苗字",
  teacher_f_kana: "教員の名前（カナ）",
  teacher_l_kana: "教員の苗字（カナ）",
  teacher_status: "管理者かどうか"
}

// リクエスト: POST
url = "/web/teachers/";
```

### ✅教員アカウントを編集
```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  teacher_id: "教員ID",
  teacher_pass: "パスワード（初期パスワード）",
  teacher_f_name: "教員の名前",
  teacher_l_name: "教員の苗字",
  teacher_f_kana: "教員の名前（カナ）",
  teacher_l_kana: "教員の苗字（カナ）",
  teacher_status: "管理者かどうか"
}

// リクエスト: PUT
url = "/web/teachers/";
```

### ✅教員IDから特定の教員アカウントを取得
```js
// 返してほしい値
{
  teacher_id: "教員ID",
  teacher_pass: "パスワード（初期パスワード）",
  teacher_f_name: "教員の名前",
  teacher_l_name: "教員の苗字",
  teacher_f_kana: "教員の名前（カナ）",
  teacher_l_kana: "教員の苗字（カナ）",
  teacher_status: "管理者かどうか"
}

// SQLで使う値
// String型 teacher_id 教員ID

// リクエスト: GET
url = "/web/teachers/{teacher_id}";
```

### ✅教員アカウントのパスワードを初期化する
```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}


// SQLで使う値
{
  student_id: "教員ID",
  student_pass: "パスワード（初期パスワード）"
}

// リクエスト: GET
url = "/web/teachers/password/";
```

---

## シラバス管理（管理者）のリクエスト

### ✅年度・学期・ステータスからシラバス一覧を取得

```js
// 返してほしい値
// androidのリクエストの「/android/register/{class_id}を参考に
[
  {
    class_id: "授業ID",
    class_name: "授業名",
    class_week_timetable: [
      {
        class_week: "実施曜日",
        class_timetable: "実施時間",
      }
    ],
    class_target_grade: [
      {
        grade: "対象学年"
      }
    ],
    teacher: {
      teacher_f_name: "教員名前",
      teacher_l_name: "教員苗字",
    },
    class_certification: "承認してるかしてないかのboolean？"
  }
]

// SQLで使う値
// int型 year 年度
// String型 season 学期
// int型 status 承認済みかどうか

// リクエスト: GET
url = "/web/syllabus/{year}/{season}/{status}";
```

### ✅授業名でシラバスを絞り込む

```js
// 返してほしい値
// androidのリクエストの「/android/register/{class_id}を参考に
[
  {
    class_id: "授業ID",
    class_name: "授業名",
    class_week_timetable: [
      {
        class_week: "実施曜日",
        class_timetable: "実施時間",
      }
    ],
    class_target_grade: [
      {
        grade: "対象学年"
      }
    ],
    teacher: {
      teacher_f_name: "教員名前",
      teacher_l_name: "教員苗字",
    },
    class_certification: "承認してるかしてないかのboolean？"
  }
]

// SQLで使う値
// int型 year 年度
// String型 season 学期
// int型 status 承認済みかどうか
// String search_items 検索ワード

// リクエスト: GET
url = "/web/syllabus/{year}/{season}/{status}/search/{search_items}";
```

### ✅class_idから1つのシラバス情報を取得

```js
// 返してほしい値
// androidのリクエストの「/android/register/{class_id}と全く同じでいいはず
// とりあえずdocsからコピペ↓
{
  "ClassSyllabus": {
    "class_id": "string",
    "class_name": "string",
    "class_color": "string",
    "class_year": 0,
    "class_season": "string",
    "required": "string",
    "class_unit": 0,
    "absence_start": 0,
    "class_outline": "string"
  },
  "faculty": {
    "faculty_name": "string"
  },
  "department": {
    "department_name": "string"
  },
  "class_week_timetable": [
    {
      "class_id": "string",
      "class_week": 0,
      "class_timetable": 0
    }
  ],
  "class_target_grade": [
    {
      "class_id": "string",
      "grade": 0
    }
  ],
  "teacher": {
    "teacher_f_name": "string",
    "teacher_l_name": "string"
  },
  "campus": {
    "campus_name": "string"
  },
  "classroom": {
    "classroom_name": "string"
  }
}

// SQLで使う値
// String型 class_id 授業ID

// リクエスト: GET  
url = "/web/syllabus/{class_id}";
```

### ✅シラバスを承認する

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  class_id: "授業ID",
  class_certification: "承認してるかしてないかのboolean？",
}

// リクエスト: PUT
url = "/web/syllabus/certification/";
```

---

## 学校情報設定 > キャンパス・教室（管理者）のリクエスト

### ✅キャンパス・教室を一覧で取得

```js
// 返してほしい値
// キャンパスの情報の中に、教室のリストが入っている形が最も理想的である
[
  {
    campus_id: "キャンパスID",
    campus_name: "キャンパス名",
    classroom_list: [
      {
        classroom_id: "教室ID",
        classroom_name: "教室名",
        classroom_floor: "階数"
      }
    ]
  }
]

// 以下の形でもReact内で上の形に書き換えることも可能ではあるはず
[
  {
    classroom_id: "教室ID",
    classroom_name: "教室名",
    classroom_floor: "階数",
    campus: {
        campus_id: "キャンパスID",
        campus_name: "キャンパス名"
      }
  }
]

// SQLで使う値
// なし

// リクエスト: GET
url = "/web/campus_classroom/";
```

### ✅キャンパスの作成

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  campus_id: "キャンパスID",
  campus_name: "キャンパス名"
}

// リクエスト: POST
url = "/web/campus_classroom/campus/";
```

### ✅キャンパスの編集

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
// idって変更できる...?
{
  campus_id: "キャンパスID",
  campus_name: "キャンパス名"
}

// リクエスト: PUT
url = "/web/campus_classroom/campus/";
```

### ✅教室の作成

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  classroom_id: "教室ID",
  classroom_name: "教室名",
  classroom_floor: "階数"
}

// リクエスト: POST
url = "/web/campus_classroom/classroom/";
```

### ✅教室の編集

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
// idって変更できる...?
{
  classroom_id: "教室ID",
  classroom_name: "教室名",
  classroom_floor: "階数"
}

// リクエスト: PUT
url = "/web/campus_classroom/classroom/";
```

---

## 学校情報設定 > 学部・学科（管理者）のリクエスト

### ✅学部・学科を一覧で取得

```js
// 返してほしい値
// 学部の情報の中に、学科のリストが入っている形が最も理想的である
[
  {
    faculty_id: "学部ID",
    faculty_name: "学部名",
    department_list: [
      {
        department_id: "学科ID",
        department_name: "学科名"
      }
    ]
  }
]

// 以下の形でもReact内で上の形に書き換えることも可能ではあるはず
[
  {
    department_id: "学科ID",
    department_name: "学科名",
    faculty: {
        faculty_id: "学部ID",
        faculty_name: "学部名"
      }
  }
]

// SQLで使う値
// なし

// リクエスト: GET
url = "/web/faculty_department/";
```

### ✅学部の作成

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  faculty_id: "学部ID",
  faculty_name: "学部名"
}

// リクエスト: POST
url = "/web/faculty_department/faculty/";
```

### ✅学部の編集

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
// idって変更できる...?
{
  faculty_id: "学部ID",
  faculty_name: "学部名"
}

// リクエスト: PUT
url = "/web/faculty_department/faculty/";
```

### ✅学科の作成

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  department_id: "学科ID",
  department_name: "学科名",
  faculty_id: "学部ID",
}

// リクエスト: POST
url = "/web/faculty_department/department/";
```

### ✅学科の編集

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
// idって変更できる...?
{
  department_id: "学科ID",
  department_name: "学科名",
  faculty_id: "学部ID",
}

// リクエスト: PUT
url = "/web/faculty_department/department/";
```

---

## 学校情報設定 > 時限数・授業時間（管理者）のリクエスト

### ✅school_infoテーブルの情報を編集

```js
// 返してほしい値
// リクエストが成功したかどうか
{
  success: "成功したか失敗したか",
  message: "エラーメッセージ"
}

// SQLで使う値
{
  school_id: "学校ID？",
  lesson_day: "平日のみ or 平日+土曜 or 平日+土日",
  first_start: "1限目開始時刻",
  first_end: "1限目終了時刻",
  second_start: "2限目開始時刻",
  second_end: "1限目終了時刻",
  third_start: "3限目開始時刻",
  third_end: "1限目終了時刻",
  fourth_start: "4限目開始時刻",
  fourth_end: "1限目終了時刻",
  deadline_early: "前期シラバス締め切り期日（MM-ddの形）",
  deadline_late: "後期シラバス締め切り期日（MM-ddの形）"
}

// リクエスト: PUT
url = "/school_info";
```

---

## ホーム（教員）のリクエスト

### 今日の予定を最大3件取得
[特定の日付の予定を取得を参照](#特定の日付の予定を取得)

### 新着のお知らせを最大3件取得
[お知らせ一覧を取得を参照](#お知らせ一覧を取得) 

---

## 授業（教員）のリクエスト

### ✅時間、授業IDから、現在の出席状況を取得

```js
// 返してほしい値
// lateness_absenceテーブルから、class_id、the_dateが一致するデータを取得
// 並び順は、created_atの降順
[
  {
      student_id: "学籍番号",
      student_f_name: "学生の名前",
      student_l_name: "学生の苗字",
      l_or_a: "出席or欠席or遅刻",
      created_at: "出席（遅刻）した日時（遅刻欠席連絡した日時）"
  }
]

// SQLで使う値
// String型 class_id 教員ID
// String型 the_date 授業実施日

// リクエスト: GET
url = "/web/class/attendance/{class_id}/{date}";
```

### ✅年度、学期、授業ID、指定の月から、出席状況一覧を取得

### ✅学生一人の出席状況を取得

```js
// 返してほしい値
// lateness_absenceから遅刻・欠席のみデータを取得
// "the_reason", "the_detail"の値によって連絡ありかをReact側で判定
[
  {
    "the_date": "日付",
    "the_time": "時限",
    "l_or_a": "遅刻or欠席",
    "the_reason": "理由",
    "the_detail": "理由詳細",
    "created_at": "作成日時",
  }
]

// SQLで使う値
// String class_id 授業ID
// int year 年度
// int month 月
// String student_id 学籍番号

// リクエスト: GET
url = "/web/class/attendance/all/{class_id}/{year}/{month}/{student_id}";
```

### ✅授業スケジュール一覧を取得
```js
// 返してほしい値
// schedule_changeテーブルから取得
// タグの情報も欲しい
[
  {
    "scuedule_change_id": "ID",
    "class_id": "授業ID",
    "change_date": "日付",
    "class_outline": "授業概要",
    "classroom_name": "教室名",
    "class_status" "実施予定かどうか",
    "tags": [
      {
        "class_tag": "タグ名"
      }
    ]
  }
]    

// SQLで使う値

// リクエスト: GET
url = "/web/schedule_change/{class_id}";
```

### ✅授業スケジュールを作成

### ✅授業スケジュールを編集

### ⚠️授業スケジュールを削除（500エラーになる）

---

## カレンダー（教員）のリクエスト

### ✅月の予定を取得
```js
// 返してほしい値
// androidのときと同じでいいと思う(teacher_idは追加で)
// student_idがnullのやつだけ
// 降順で
[
  {
    "schedule_id": "予定ID",
    "schedule_title": "タイトル",
    "schedule_start": "開始日時",
    "schedule_end": "終了日時",
    "schedule_text": "詳細",
    "schedule_color": "予定カラー",
    "place": "場所",
    "class_info": {
      "class_name": "授業名",
      "class_color": "授業カラー"
    },
    "classroom": {
      "campus": {
        "campus_name": "キャンパス名"
      },
      "classroom_name": "教室"
    },
    "teacher": {
      "teacher_id": "教員ID",
      "teacher_f_name": "教員名前",
      "teacher_l_name": "教員苗字"
    }
  }
]

// SQLで使う値
// int year 年度
// int month 学期

// リクエスト: GET
url = "/web/schedule/{year}/{month}";
```

### ✅特定の日付の予定を取得
```js
// 返してほしい値
// androidのときと同じでいいと思う(teacher_idは追加で)
// student_idがnullのやつだけ
// 降順で
[
  {
    "schedule_id": "予定ID",
    "schedule_title": "タイトル",
    "schedule_start": "開始日時",
    "schedule_end": "終了日時",
    "schedule_text": "詳細",
    "schedule_color": "予定カラー",
    "place": "場所",
    "class_info": {
      "class_name": "授業名",
      "class_color": "授業カラー"
    },
    "classroom": {
      "campus": {
        "campus_name": "キャンパス名"
      },
      "classroom_name": "教室"
    },
    "teacher": {
      "teacher_id": "教員ID",
      "teacher_f_name": "教員名前",
      "teacher_l_name": "教員苗字"
    }
  }
]

// SQLで使う値
// String date 日付（yyyy-MM-ddの形）

// リクエスト: GET
url = "/web/schedule/schedule/{date}/";
```

### ✅予定IDから、特定の予定の詳細を取得
```js
// 返してほしい値
{
  "schedule_id": "予定ID",
  "schedule_title": "タイトル",
  "schedule_start": "開始日時",
  "schedule_end": "終了日時",
  "schedule_text": "詳細",
  "schedule_color": "予定カラー",
  "place": "場所",
  "class_info": {
    "class_name": "授業名",
    "class_color": "授業カラー"
  },
  "classroom": {
    "campus": {
      "campus_name": "キャンパス名"
    },
    "classroom_name": "教室"
  },
  "teacher": {
    "teacher_id": "教員ID",
    "teacher_f_name": "教員名前",
    "teacher_l_name": "教員苗字"
  }
}

// SQLで使う値
// String schedule_id 予定ID

// リクエスト: GET
url = "/web/schedule/{schedule_id}/";
```


### □予定を作成
```js
// 返してほしい値
// 成功したかどうか

// SQLで使う値
{
  "teacher_id": "教員ID",
  "schedule_title": "タイトル",
  "schedule_start": "開始日時",
  "schedule_end": "終了日時",
  "schedule_text": "詳細",
  "schedule_color": "予定カラー",
  "place": "場所",
  "class_id": "授業ID"
  "classroom_id": "教室ID",
}

// リクエスト: POST
url = "/web/schedule/";
```

### □予定を編集
```js
// 返してほしい値
// 成功したかどうか

// SQLで使う値
{
  "schedule_id", "予定ID"
  "teacher_id": "教員ID",
  "schedule_title": "タイトル",
  "schedule_start": "開始日時",
  "schedule_end": "終了日時",
  "schedule_text": "詳細",
  "schedule_color": "予定カラー",
  "place": "場所",
  "class_id": "授業ID"
  "classroom_id": "教室ID",
}

// リクエスト: PUT
url = "/web/schedule/";
```

### □予定を削除
```js
// 返してほしい値
// 成功したかどうか

// SQLで使う値
// int schedule_id 予定ID

// リクエスト: DELETE
url = "/web/schedule/{schedule_id}/";
```

### □予定の検索
```js
// 返してほしい値
// student_idがnullのやつだけ
[
  {
    "schedule_id": "予定ID",
    "schedule_title": "タイトル",
    "schedule_start": "開始日時",
    "schedule_end": "終了日時",
    "schedule_text": "詳細",
    "schedule_color": "予定カラー",
    "place": "場所",
    "class_info": {
      "class_name": "授業名",
      "class_color": "授業カラー"
    },
    "classroom": {
      "campus": {
        "campus_name": "キャンパス名"
      },
      "classroom_name": "教室"
    },
    "teacher": {
      "teacher_id": "教員ID",
      "teacher_f_name": "教員名前",
      "teacher_l_name": "教員苗字"
    }
  }
]

// SQLで使う値
// String search_items 検索ワード

// リクエスト: GET
url = "/android/schedule/search/{search_items}/";
```

---

## お知らせ（教員）のリクエスト

### ✅公開お知らせ一覧を取得
### □非公開お知らせ一覧を取得（あとでDBにデータ追加して確認）

### ✅特定のお知らせの、学生の既読状態を見る
```js
// 返してほしい値

// SQLで使う値

// リクエスト: GET
url = "";
```

### お知らせを作成
```js
// 返してほしい値

// SQLで使う値

// リクエスト: GET
url = "";
```

### お知らせを編集
```js
// 返してほしい値

// SQLで使う値

// リクエスト: GET
url = "";
```

### お知らせを削除
```js
// 返してほしい値

// SQLで使う値

// リクエスト: GET
url = "";
```

---

## シラバス（教員）のリクエスト

### シラバス作成
```js
// 返してほしい値
// 成功したかどうか

// SQLで使う値
// 作成されたとき、class_certificationはfalseにしておく
{
  class_name: "授業名",
  teacher_id: "担当教員ID",
  classroom_id: "実施教室",
  faculty_id: "対象の学部ID",
  department_id: "対象の学科ID",
  class_unit: "単位数",
  class_color: "授業カラー",
  class_year: "年度",
  class_season: "学期",
  required: "必修か",
  absent_start: "何分から遅刻か",
  class_outline: "授業概要",
  grade: ["対象の学年"],
  class_week_timetable: [
    {
      class_week: "対象の曜日",
      class_timetables: [
        "対象の曜日に実施される時間"
      ]
    }
  ]
}

// リクエスト: POST
url = "/web/teacher/syllabus/";
```

### 年度学期からシラバスの情報を取得
```js
// 返してほしい値

// SQLで使う値

// リクエスト: GET
url = "";
```

### 受講学生一覧を取得
```js
// 返してほしい値

// SQLで使う値

// リクエスト: GET
url = "";
```