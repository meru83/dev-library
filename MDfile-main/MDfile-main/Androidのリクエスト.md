# Androidのリクエスト内容

## LoginActivity
### ✅ログイン
```java
// SQLで使う値
public class LoginRequest {
    private String studentId;
    private String password;
}

// 返してほしい値
public class LoginResponse {
    private String accessToken;
    private String tokenType;
    private String studentId;
}

// リクエスト：POST
public interface ApiService {
    @POST("/login")
    // @POST("/token")  
    Call<LoginResponse> login(@Body LoginRequest loginRequest);
}
```

学籍番号とパスワードが一致するときだけ値を返す
```sql
SELECT student_id FROM student WHERE student_id = 'test_student_id' AND password = 'test_password';
```

## MyPageActivity
### ✅マイページ表示のための学生データ取得
```java
// 返してほしい値
public class StudentData {
    String studentId;       // 学籍番号
    String lastName;        // 苗字
    String firstName;       // 名前
    String faculty;         // 学部名
    String department;      // 学科名
    int grade;              // 学年
    String iconPath;        // プロフィールアイコンのパス（これは変更可能）
}

// sqlで使う値：studentId（学籍番号）

public interface ApiService {
    @GET("/students/{student_id}")
    Call<StudentData> getStudentData(@Path("student_id") String student_id);
}
```

## ウィジェット
### ✅今日の時間割を取得
```java
// 返してほしい値
// 時限順に出してほしい
public class TimetableItem {
    String classId;        // 授業ID
    String className;      // 授業名
    String color;          // 授業の色
}

// sqlで使う値
// String型 studentId（学籍番号）
// int型 weekday（本日の曜日の数字。こっちから今日の曜日を確認して数値を送ります）

// リクエスト: GET
public interface ApiService {
    @GET("students/{student_id}/timetable/{weekday}")
    Call<List<TimetableItem>> getTodayTimetable(@Path("student_id") String student_id, @Path("weekday") int weekday);
}
```

## 出席など
### ✅事前の遅刻欠席連絡を送信
```java
// 返してほしい値
// 特になし。リクエストが成功したかどうかは知りたい

// sqlで使う値
public class LatenessAbsenceContact {
    String student_id;  // 学籍番号
    String the_date;    // 日付
    String the_time;    // 何時限目か
    String class_id;    // 授業ID
    int l_or_a;         // 遅刻か欠席か
    String the_reason;  // 理由（こっちでプルダウンで選択し、その文字列を渡す）
    String the_detail;  // 理由詳細
}

// リクエスト: POST
public interface ApiService {
    @POST("/android/latenessAbsence")
    Call<> sendLatenessAbsenceContact(@Body LatenessAbsenceContact latenessAbsenceContact);
}
```
## 履修登録関係
### ✅曜日と時限を選択したときに授業（シラバス）一覧を表示するリクエスト

```java
// 返して欲しい値
public class ClassItem {
    Steing id;            // 授業ID
    String class_name;    // 授業名
    String teacher_name;  // 教員名
    int unit;             // 単位数
    List<int> time;             // 何限にあるのか（複数あるなら同じ曜日のものを全部取得してほしい）
    int required;         // 必修か選択か
}

// SQLで使う値
// String型 学籍番号（から学部ID・学科IDを取得して対象のシラバスを取得）
// int型 年度
// String型 学期
// int型 曜日
// int型 時限

// リクエスト: GET
public interface ApiService {
    @GET("/android/student/{student_id}/class/{year}/{season}/{week}/{time}")
    Call<List<ClassItem>> getWeekTimeSyllabus(
    @Path("student_id") String student_id,
    @Path("year") int year,
    @Path("season") String season,
    @Path("week") int week,
    @Path("time") int time
    );
```

### ✅シラバス情報を取得

```java
// 返して欲しい値
{
    class_id: "授業ID",
    class_name: "授業名",
    class_color: "授業カラー",
    faculty: {
        faculty_name: "対象の学部名"
    },
    department: {
        department_name: "対象の学科名"
    },
    class_year: "年",
    class_season: "前期or後期",
    class_unit: "単位数",
    teacher: {
        teacher_name: "担当の教員名"
    },
    class_week_time: [
        {
            class_week: "実施曜日"
            class_timetable: "実施時間"
        }   // 複数の可能性もあるため、配列が理想...
    ],
    campus: {
        campus_name: "キャンパス名"
    },
    classroom_id: {
        classroom_name: "教室名"
    },
    absence_start:  "何分後から欠席か",
    class_outline:  "授業概要"
}

// SQLで使う値
// String型 授業ID

// リクエスト: GET
public interface ApiService {
    @GET("/android/register/{class_id}")
    Call<SyllabusItem> getWeekTimeSyllabus(@Path("class_id") String class_id);
```

### ✅履修登録の送信
```java
// 返してほしい値
// 特になし。リクエストが成功したかどうか

// SQLで使う値（class_registerテーブルのカラムと同じ）
public class CourseRegPostItem {
    String student_id;
    String class_id;
    int register_week;
    int register_time;
    int register_year;
    String register_season;
}

// リクエスト: POST
public interface ApiService {
    @POST("/android/register")
    Call<> postRegisterList(@Body List<CourseRegPostItem> courseRegPostItemList);
```

## 学校情報について
### ✅学校情報（school_infoテーブルのすべての値）を取得


## ScheduleFragment
### ✅特定の"日"の予定のリストを取得
```java
// 返してほしい値
public class Schedule {
    private String id;                      // スケジュールのID
    
    private String title;                   // タイトル

    private String startDateTime;           // 開始日時
    private String endDateTime;             // 終了日時

    private String place;                   // 教室名
    private String details;                 // 詳細

    private String studentId;               // 学籍番号(個人予定の場合のみ。他はNULL)
    private String class_id;                // 授業ID(授業予定の場合のみ。他はNULL)
    private String colorId;                 // 予定カラー(授業予定の場合、授業カラー)
}

// SQLで使う値
// String型 学籍番号
// int型 指定する年
// int型 指定する月
// int型 指定する日

public interface ApiService {
    @GET("/android/students/{student_id}/schedule/{year}/{month}/{date}/")
    Call<List<Schedule>> getScheduleByDate(@Path("student_id") String student_id, @Path("year") int year, @Path("month") int month, @Path("date") int date);
}
```

### ✅特定の"月"の予定のリストを取得(保留)
```java
// 返してほしい値
// ↑のやつと一緒

// SQLで使う値
// String型 学籍番号
// int型 指定する年
// int型 指定する月

public interface ApiService {
    @GET("/android/students/{student_id}/schedule/{year}/{month}/")
    Call<List<Schedule>> getScheduleByMonth(@Path("student_id") String student_id, @Path("year") int year, @Path("month") int month);
}
```

### ✅学生個人の予定を登録
```java
// 返してほしい値
// 特になし。成功したかどうか。

// SQLで使う値
public class PostScheduleData {
    String student_id
    String schedule_title;
    String schedule_start;
    String schedule_end;
    String schedule_text;
    String schedule_color;
}

public interface ApiService {
    @POST("/android/schedule/")
    Call<ResponseData> postStudentSchedule(@Body PostScheduleData postScheduleData);
}     
```

### ✅学生個人の予定を編集
```java
// 返してほしい値
// 特になし。成功したかどうか。

// SQLで使う値
public class PostScheduleData {
    String schedule_id;
    String student_id;
    String schedule_title;
    String schedule_start;
    String schedule_end;
    String schedule_text;
    String schedule_color;
}

public interface ApiService {
    @PUT("/android/schedule/")
    Call<ResponseData> postStudentSchedule(@Body PostScheduleData postScheduleData);
}     
```

### ✅学生個人の予定を削除
```java
// 返してほしい値
// 特になし。成功したかどうか。

// SQLで使う値
// String型 予定ID

public interface ApiService {
    @DELETE("/android/schedule/{schedule_id}")
    Call<ResponseData> postStudentSchedule(@Path("schedule_id") String schedule_id);
}     
```

### ✅予定IDから特定の予定だけ取得
```java
// 返してほしい値
// 他の予定取得と同じ
// できたら配列じゃなくて、`{}`で返してほしい
  {
    "schedule_id": "予定ID",
    "schedule_title": "タイトル",
    "schedule_start": "開始日時",
    "schedule_end": "終了日時",
    "schedule_text": "詳細",
    "schedule_color": "予定カラー（学生個人の予定のみ）",
    "place": "場所（学生個人の予定のみ）",
    "class_info": {
      "class_name": "授業名",
      "class_color": "授業カラー"
    },
    "classroom": {
      "campus": {
        "campus_name": "キャンパス名"
      },    
      "classroom_name": "教室名"
    },
    "teacher": {
      "teacher_f_name": "教員名",
      "teacher_l_name": "教員苗字"
    }
  }

// SQLで使う値
// String型 schedule_id

public interface ApiService {
    @GET("/android/students/{schedule_id}")
    Call<Schedule> getScheduleById(@Path("schedule_id") String schedule_id);
}
```

### □絞り込んだ予定のリストを取得←`NEW!!!`
```java
// 返してほしい値
// 他の予定取得と同じ。複数取得なのでリスト`[]`の形で。
// 検索ワードと、タイトル、場所、詳細いずれかと部分一致するものだけ返す。
[
  {
    "schedule_id": "予定ID",
    "schedule_title": "タイトル",
    "schedule_start": "開始日時",
    "schedule_end": "終了日時",
    "schedule_text": "詳細",
    "schedule_color": "予定カラー（学生個人の予定のみ）",
    "place": "場所（学生個人の予定のみ）",
    "class_info": {
      "class_name": "授業名",
      "class_color": "授業カラー"
    },
    "classroom": {
      "campus": {
        "campus_name": "キャンパス名"
      },    
      "classroom_name": "教室名"
    },
    "teacher": {
      "teacher_f_name": "教員名",
      "teacher_l_name": "教員苗字"
    }
  }
]

// SQLで使う値
// String型 学籍番号
// String型 検索ワード

// リクエスト: GET
public interface ApiService {
    @GET("/android/schedule/{schedule_id}/{search_word}")
    Call<List<Schedule>> getSortedSchedules(@Path("schedule_id") String schedule_id, @Path("search_word") String search_word);
}
```


## お知らせ関係
### ✅すべての(publicの)お知らせを取得
```java
// 返してほしい値
// リストで
[
    {
        notice_id: "お知らせID",
        notice_title: "お知らせタイトル",
        notice_text: "お知らせ本文",
        notice_file_url: "ファイルのURL?",
        faculty: {
            faculty_name: "対象学部名"
        },
        department: {
            department_name: "対象学科名"
        },
        create_at: "作成日時",
        already: "既読かどうか（真偽値）",
        bookmark: "ブックマークしたかどうか（真偽値）",
        teacher: {
            teacher_f_name: "教員の名前",
            teacher_l_name: "教員の苗字"
        }, // ←追加
    },
]

// SQLで使う値
// String型 学籍番号

public interface ApiService {
    @GET("/android/notices/{student_id}")
    Call<List<NoticeItem>> getNotices(@Path("schedule_id") String schedule_id);
}
```

### ✅お知らせIDから一つだけお知らせを取得
```java
// 返してほしい値
// ↑と同じ

// SQLで使う値
// String型 お知らせID

public interface ApiService {
    @GET("/android/notices/{notice_id}")
    Call<NoticeItem> getNoticeById(@Path("notice_id") String notice_id);
}
```

### ✅お知らせを既読にする

### ✅お知らせをブックマークする

### ✅お知らせのブックマークを削除する

### □絞り込んだお知らせのリストを取得←`NEW!!!`
```java
// 返してほしい値
// 他のお知らせ取得と同じ。複数取得なのでリスト`[]`の形で。
// 検索ワードと、タイトル、お知らせ本文、対象学部名、対象学科名、教員の名前、教員の苗字いずれかと部分一致するものだけ返す。
[
    {
        notice_id: "お知らせID",
        notice_title: "お知らせタイトル",
        notice_text: "お知らせ本文",
        notice_file_url: "ファイルのURL?",
        faculty: {
            faculty_name: "対象学部名"
        },
        department: {
            department_name: "対象学科名"
        },
        create_at: "作成日時",
        already: "既読かどうか（真偽値）",
        bookmark: "ブックマークしたかどうか（真偽値）",
        teacher: {
            teacher_f_name: "教員の名前",
            teacher_l_name: "教員の苗字"
        }
    },
]

// SQLで使う値
// String型 学籍番号
// String型リスト 検索ワード

// リクエスト: GET
public interface ApiService {
    @GET("/android/schedule/{notice_id}/{search_word}")
    Call<List<Notification>> getSortedNotice(@Path("notice_id") String notice_id, @Path("search_word") String search_word);
}
```

## 時間割
### ✅今期履修しているすべての授業を取得
```java
// 返してほしい値
// 月曜の1限、火曜の1限...金曜の1限、月曜の2限、火曜の2限...という順番にしてほしい
public class TimetableItem {
    String class_id;        // 授業ID
    String class_name;      // 授業名
    String class_color;     // 授業の色
}

// sqlで使う値
// String型 studentId（学籍番号）
// int型 register_year（年度）
// String型 register_season（学期）

// リクエスト: GET
public interface ApiService {
    @GET("/android/widget/students/{student_id}/timetable/{register_year}/{register_season}/")
    Call<List<TimetableItem>> getTimetable(@Path("student_id") String student_id, @Path("register_year") int register_year, @Path("register_season") String register_season);
}
```

### □授業IDと時限と日付とから、特定の授業の授業スケジュールを1つ取得 `← NEW!!`
```java
// 返してほしい値
// リストじゃなくて`{}`の形で
// schedule_changeテーブルから、授業ID、日付、キャンパス名と教室、休講の状態
// schedule_tagテーブルから、タグのリスト
{
    "class_id": "授業ID",
    "date": "日付",
    "classroom": {
        "classroom_name": "教室名"
    },
    "campus": {
        "campus_name": "キャンパス名"
    },
    "status" "休講か実施予定か",
    "tags": [
        tag_name: "タグ名"
    ],
}

// sqlで使う値
// String型 classId（授業ID）
// int型 change_time（時限）
// int型 date（日付）

// リクエスト: GET
// パス考えてないです
```

### □授業IDから、特定の授業の授業スケジュールのリストを取得 `← NEW!!`
```java
// 返してほしい値
// リスト`[]`の形で
// schedule_changeテーブルから、授業ID、日付、キャンパス名と教室、休講の状態
// schedule_tagテーブルから、タグのリスト
{
    "class_id": "授業ID",
    "date": "日付",
    "classroom": {
        "classroom_name": "教室名"
    },
    "campus": {
        "campus_name": "キャンパス名"
    },
    "status" "休講か実施予定か",
    "tags": [
        tag_name: "タグ名"
    ],
}

// sqlで使う値
// String型 class_id（授業ID）

// リクエスト: GET
// パス考えてないです
```

### ✅学生IDと授業IDから、特定の授業の出席データを取得
```java
// 返してほしい値
// リスト`[]`の形で
// lateness_absenceテーブルから、日付、時限、出席or欠席or遅刻
[
    {
    "date": "日付",
    "the_time": "時限",
    "l_or_a" "出席or欠席or遅刻"
    }
]

// sqlで使う値
// String型 student_id（学生ID）
// String型 class_id（授業ID）

// リクエスト: GET
// パス考えてないです
```

### ✅学生IDと授業IDから、特定の授業の出席データ(回数)を取得
```java
// 返してほしい値
// lateness_absenceテーブルから、出席の回数、欠席の回数、遅刻の回数、今までの授業数(前3つの合計)
// 今までの授業数(前3つの合計)は計算したら出せるからいらんかも
{
    "atendance_cnt": "出席の回数",
    "absence_cnt": "欠席の回数",
    "lateness_cnt" "遅刻の回数",
    "sum_cnt": "今までの授業数"
}

// sqlで使う値
// String型 student_id（学生ID）
// String型 class_id（授業ID）

// リクエスト: GET
// パス考えてないです
```
