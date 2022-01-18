# utl-a-sas-view-of-my-issues-with-python-and-r
SAS view of my issues with python and r 
    %let pgm=utl-a-sas-view-of-my-issues-with-python-and-r;

    SAS view of my issues with python and r

    Maybe it is me and not R or Python

    github
    https://tinyurl.com/2p9c4auu
    https://github.com/rogerjdeangelis/utl-a-sas-view-of-my-issues-with-python-and-r

    The biggest issue I have with R and Python is exporting R and Python for use with other languages.
    The problem is not with R or Python Programming, the problem is conditioning the output from R and Python
    for use with another language. Note, this problem is easily solved within Python.

    There appear to be dozens/hundreds of data structures and dozens data types in R and Python.
    Many of these structures are very difficult to translate to generic data structures and data types?.

    I am not a fan of CSVs as a means of communication. Python and R programmers seem to favor CSVs?
    I am also not a fan of Klingon Python and R programming.

    Consider this simple example of trying to export the python object below to SAS, same issues with R.
    I use SQL in R and Python because it inputs and outputs dataframes that can to shared with other languages.

      A failed and a successful solution

           1. Failed non SQL Python solution
           2. Successful SQL Python solution

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    libname sd1 "d:/sd1";

    * Problem
        Player Number 23 should not have registered twice for the colts.
        We need to remove one of the registrations;

    Options validvarname=upcase;

    data sd1.have;
    input
      TEAM  $  PLAYER;
    cards4;
    ASTROS 15
    ASTROS 29
    ASTROS .
    ASTROS .
    BRAVES 19
    BRAVES .
    BRAVES .
    BRAVES .
    COLTS 44
    COLTS 23
    COLTS 36
    COLTS 23
    CUBS 27
    CUBS  .
    CUBS 34
    CUBS 48
    ;;;;
    run;quit;

    Up to 40 obs SD1.HAVE total obs=16 15JAN2022:09:54:52

                             |  RULES
    Obs     TEAM     PLAYER  |
                             |
      1    ASTROS      15    |
      2    ASTROS      29    |
      3    ASTROS       .    |
      4    ASTROS       .    |
      5    BRAVES      19    |
      6    BRAVES       .    |
      7    BRAVES       .    |
      8    BRAVES       .    |
      9    COLTS       44    |
     10    COLTS       23    |
     11    COLTS       36    |
     12    COLTS       23    |  Remove this duplicate registration for Player 23
     13    CUBS        27    |
     14    CUBS         .    |
     15    CUBS        34    |
     16    CUBS            |

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

       TEAM     PLAYER1    PLAYER2    PLAYER3    PLAYER4

      ASTROS       15         29          .          .
      BRAVES       19          .          .          .
      COLTS        44         23         36          .  * 23 appears once
      CUBS         27          .         34         48


    /*
     _ __  _ __ ___   ___ ___  ___ ___  ___  ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|/ _ \/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \  __/\__ \
    | .__/|_|  \___/ \___\___||___/___/\___||___/
    |_|
     _     _____     _ _          _   ____        _   _
    / |   |  ___|_ _(_) | ___  __| | |  _ \ _   _| |_| |__   ___  _ __
    | |   | |_ / _` | | |/ _ \/ _` | | |_) | | | | __| `_ \ / _ \| `_ \
    | |_  |  _| (_| | | |  __/ (_| | |  __/| |_| | |_| | | | (_) | | | |
    |_(_) |_|  \__,_|_|_|\___|\__,_| |_|    \__, |\__|_| |_|\___/|_| |_|
                                            |___/
    */

    %utl_submit_py64_39("
    from os import path;
    import pandas as pd;
    import xport;
    import xport.v56;
    import pyreadstat;
    import numpy as np;
    from pandasql import sqldf;
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    have, meta = pyreadstat.read_sas7bdat('d:/sd1/havlon.sas7bdat');
    have.info();
    have = have.replace(r'^\s*$', np.NA, regex=True);
    have['IDX'] = res.groupby('TEAM').iccount();
    have.info();
    print(have);
    ");

    * Tried some of the statements most fail.
    * I tried many more

    This is what I get from SAS in Python

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 12 entries, 0 to 11
    Data columns (total 2 columns):
     #   Column  Non-Null Count  Dtype
    ---  ------  --------------  -----
     0   TEAM    12 non-null     object
     1   PLAYER  12 non-null     object
    dtypes: object(2)
    memory usage: 320.0+ bytes


    * Tried to replace the missings with NA;
    have = have.replace(r'^\s*$', np.NA, regex=True);
    AttributeError: module 'numpy' has no attribute 'NA'

    * this appears to work, but too much Klingon for me;
    * begins and end with a space;
    have = have.replace(r'^\s*$', np.NaN, regex=True);

    * lets se if we can change player column to float

    * lets see if we can change type to intrger;
    have['PLAYER'] = have['PLAYER'].astype(int)
    ValueError: invalid literal for int() with base 10: ''

    * lets see if we can add a seqience number to the repeating
      rows so we can pivot;
      have['IDX'] = res.groupby('TEAM').iccount();

    * never got far enough for the
    xpo=have.pivot(index='TEAM',columns='PLAYER', values='VAL')

    /*__         _
    |_ _|   __ _(_)_   _____   _   _ _ __
     | |   / _` | \ \ / / _ \ | | | | `_ \
     | |  | (_| | |\ V /  __/ | |_| | |_) |
    |___|  \__, |_| \_/ \___|  \__,_| .__/
           |___/                    |_|
    */
    I GIVE UP

    HERE IS THE ELEGANT NON SQL SOLUTION IN
    IT HIGHLIGHTS THE FUNCTIONALITY THAT SAL IS LACKING





    * first problem;
    have['PLAYER']=have['PLAYER'].apply(lambda x: float(x));
    ValueError: could not convert string to float: ''


    * lets see if we can drop the missings;
    have.dropna(subset = ['PLAYER'], inplace=True);
    * no error but it did not work;

    /*___
    |___ \     _ __  _   _   ___ _   _  ___ ___ ___  ___ ___
      __) |   | `_ \| | | | / __| | | |/ __/ __/ _ \/ __/ __|
     / __/ _  | |_) | |_| | \__ \ |_| | (_| (_|  __/\__ \__ \
    |_____(_) | .__/ \__, | |___/\__,_|\___\___\___||___/___/
              |_|    |___/
    */

    proc datasets lib=work kill;
    run;quit;

    %array(_s,values=1-4);

    %utlfkil(d:/xpt/want_pydat.xpt);

    %utl_submit_py64_39("
    from os import path;
    import pandas as pd;
    import xport;
    import xport.v56;
    import pyreadstat;
    import numpy as np;
    from pandasql import sqldf;
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    have, meta = pyreadstat.read_sas7bdat('d:/sd1/have.sas7bdat');
    print(have);
    havUnq=pdsql('''select distinct TEAM, PLAYER from have''');
    print(havUnq);
    havPar=pdsql('''select TEAM, PLAYER, row_number() over (partition by TEAM) as CUMCNT from havUnq''');
    print(havPar);
    havXpo=pdsql('''
      select TEAM, %do_over(_S,phrase=max(case when CUMCNT=?_S then PLAYER else null end) as PLAYER?_S,between=comma)
      from
          havPar
      group
          by TEAM
    ;''');
    print(havXpo);
    havXpo.info();
    ds = xport.Dataset(havXpo, name='want_py');
    with open('d:/xpt/want_pydat.xpt', 'wb') as f: xport.v56.dump(ds, f);
    ");

    libname xpt xport "d:/xpt/want_pydat.xpt";

    proc contents data=xpt._all_;
    run;quit;

    proc print data=xpt.WANT_PY;
    run;quit;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */


