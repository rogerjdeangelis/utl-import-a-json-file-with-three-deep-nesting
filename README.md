# utl-import-a-json-file-with-three-deep-nesting
Import a json file with three deep nesting
    Import a json file with three deep nesting

    Python and R list structures can easily flatten nested data structures.

    github
    https://github.com/rogerjdeangelis/utl-import-a-json-file-with-three-deep-nesting

    StackOverflow
    https://tinyurl.com/y4yxzu9r
    https://stackoverflow.com/questions/56940994/convert-json-file-to-dataframe-using-rjsonio-package-r

    Data Munger
    https://stackoverflow.com/users/2826124/data-munger

    other web and json repos

    https://tinyurl.com/y4x4dy8r
    https://github.com/rogerjdeangelis?utf8=%E2%9C%93&tab=repositories&q=json+in%3Aname&type=&language=


    https://tinyurl.com/y2opsr5a
    https://github.com/rogerjdeangelis?utf8=%E2%9C%93&tab=repositories&q=scrape+in%3Aname&type=&language=


    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    filename ft15f001 "d:/json/thee_deep.json";
    parmcards4;
    [
      {"id": "abc",
        "model": "honda",
        "date": "20190604",
        "cols": {"action": 15, "values": 18, "not": 29}},
      {"id": "abc",
        "model": "honda",
        "date": "20190604",
        "cols": {"hello": 14, "hi": 85, "wow": 14}},
      {"id": "mno",
        "model": "ford",
        "date": "20190605",
        "cols": {"yesterday": 21, "today": 21, "tomorrow": 29}},
      {"id": "mno",
        "model": "ford",
        "date": "20190605",
        "cols": {"docs": 25, "ok": 87, "none": 42}}
    ]
    ;;;;
    run;quit;

    *           _
     _ __ _   _| | ___  ___
    | '__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    ;

    At the deepest level we have (all distinct)

       action         15
       values         18
       not            29

       hello          14
       hi             85
       wow            14

       yesterday      21
       today          21
       tomorrow       29

       docs           25
       ok             87
       none           42

    Appling the second deepest  we have

      honda 20190604
      honda 20190604
      ford  20190605
      ford  20190605

      MODEL      DATE      COLS         VALUES

      honda    20190604    action         15   * note model and date are repeated 3 times
      honda    20190604    values         18
      honda    20190604    not            29

      honda    20190604    hello          14
      honda    20190604    hi             85
      honda    20190604    wow            14

      ford     20190605    yesterday      21
      ford     20190605    today          21
      ford     20190605    tomorrow       29

      ford     20190605    docs           25
      ford     20190605    ok             87
      ford     20190605    none           42


    Appling the initial nesting

       ID     MODEL      DATE      COLS         VALUES

       abc    honda    20190604    action         15   * abc is repeated 6 times
       abc    honda    20190604    values         18
       abc    honda    20190604    not            29

       abc    honda    20190604    hello          14
       abc    honda    20190604    hi             85
       abc    honda    20190604    wow            14

       mno    ford     20190604    yesterday      21   * mno is repeated 6 times
       mno    ford     20190604    today          21
       mno    ford     20190604    tomorrow       29

       mno    ford     20190604    docs           25
       mno    ford     20190604    ok             87
       mno    ford     20190604    none           42

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT total obs=12

    Obs    ID     MODEL      DATE      COLS         VALUES

      1    abc    honda    20190604    action         15
      2    abc    honda    20190604    values         18
      3    abc    honda    20190604    not            29
      4    abc    honda    20190604    hello          14
      5    abc    honda    20190604    hi             85
      6    abc    honda    20190604    wow            14
      7    mno    ford     20190604    yesterday      21
      8    mno    ford     20190604    today          21
      9    mno    ford     20190604    tomorrow       29
     10    mno    ford     20190604    docs           25
     11    mno    ford     20190604    ok             87
     12    mno    ford     20190604    none           42


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    filename ft15f001 "d:/json/thee_deep.json";
    parmcards4;
    [
      {"id": "abc",
        "model": "honda",
        "date": "20190604",
        "cols": {"action": 15, "values": 18, "not": 29}},
      {"id": "abc",
        "model": "honda",
        "date": "20190604",
        "cols": {"hello": 14, "hi": 85, "wow": 14}},
      {"id": "mno",
        "model": "ford",
        "date": "20190605",
        "cols": {"yesterday": 21, "today": 21, "tomorrow": 29}},
      {"id": "mno",
        "model": "ford",
        "date": "20190605",
        "cols": {"docs": 25, "ok": 87, "none": 42}}
    ]
    ;;;;
    run;quit;

    %utlfkil("d:/xpt/want.xpt");
    %utl_submit_r64('
    library(jsonlite);
    library(data.table);
    library(tidyverse);
    library(SASxport);
    have <- fromJSON("d:/json/thee_deep.json");

    want <- map_df(seq_along(have),
             ~tibble(id = have$id[.x],
                     model = have$model[.x],
                     date = have$date[.x],
                     cols = names(have$cols),
                     values = sapply(have$cols, "[", .x)
             )
    );
    want<-as.data.frame(want[complete.cases(want), ]);
    head(want);
    str(want);
    write.xport(want,file="d:/xpt/want.xpt");
    ');

    libname xpt xport "d:/xpt/want.xpt";
    data want;
      set xpt.want;
    run;quit;
    libname xpt clear;


