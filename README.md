# utl-is-the-file-a-sas-v5-transport-or-a-cport-file-or-is-the-transport-file-corrupted
Is the file a sas v5 transport, cport or is the transport file corrupted

    Is the file a sas v5 transport, cport or is the tranport file corrupted.

    I will focus on analysis of sas transort file without sas;

    Notes:
      1. It is now possible to convert a v5 sas transport file into R dataframes,
         sas datasets or panda dataframes without a sas license using Python and/or R.
      2. SAS V5 transport files can be opened in the SAS universal viewer. ( used to be able to save as csvs)
         I may have a download of the original SAS universal viewer that supports conversion of v5 transports to csv.
      3. Python pyreadstat seems to have issues importing v5 transport files. But we can create a SAS dataset or
         an RDA or RDS R file using Rand then convert the files or sas datasets into python panda dataframes.
    You will need to change the paths in some of the code below.
      Look for d:/xpt

    All the macro sas code below is located at  'https://tinyurl.com/y9nfugth'


    *    _            __ _     _           _  __       _
      __| |_ __ __ _ / _| |_  | |__   __ _| |/ _|   __| | ___  _ __   ___
     / _` | '__/ _` | |_| __| | '_ \ / _` | | |_   / _` |/ _ \| '_ \ / _ \
    | (_| | | | (_| |  _| |_  | | | | (_| | |  _| | (_| | (_) | | | |  __/
     \__,_|_|  \__,_|_|  \__| |_| |_|\__,_|_|_|    \__,_|\___/|_| |_|\___|
    ;
    *                _             _
      ___ ___  _ __ | |_ ___ _ __ | |_ ___
     / __/ _ \| '_ \| __/ _ \ '_ \| __/ __|
    | (_| (_) | | | | ||  __/ | | | |_\__ \
     \___\___/|_| |_|\__\___|_| |_|\__|___/
         _           ____               _
        / |   __   _| ___|  __  ___ __ | |_
        | |   \ \ / /___ \  \ \/ / '_ \| __|
        | |_   \ V / ___) |  >  <| |_) | |_
        |_(_)   \_/ |____/  /_/\_\ .__/ \__|
                                 |_|

           1. V5 transport file using proc copy
             R
              a. creating v5 transport file using sas proc copy with three entries.
              b. If you cannot create the v5 transport file you can download it form GitHub.
              c. Header meta data in the v5 transport file.
              d. Contents of corrupt v5 transport file
              e. Reading the v5 transport file using R and creating R and  dataframes and SAS datasets
              f. importing sas v5 transport file using sas
        *____                            _
        |___ \      ___ _ __   ___  _ __| |_
          __) |    / __| '_ \ / _ \| '__| __|
         / __/ _  | (__| |_) | (_) | |  | |_
        |_____(_)  \___| .__/ \___/|_|   \__|
                       |_|

              a. creating cport file with with 3 entries
              b. If you cannot create the cport transport file you can download it form GitHub.
              c. Header meta data in the vcport transport file (using the default compression).
              d. Contents of corrupt cport file
              e. Importing sas cport file using sas

    *_          ____               _
    / |  __   _| ___|  __  ___ __ | |_
    | |  \ \ / /___ \  \ \/ / '_ \| __|
    | |_  \ V / ___) |  >  <| |_) | |_
    |_(_)  \_/ |____/  /_/\_\ .__/ \__|
      __ _     _ __ ___   __|_| | _____  __  ___ __ | |_
     / _` |   | '_ ` _ \ / _` | |/ / _ \ \ \/ / '_ \| __|
    | (_| |_  | | | | | | (_| |   <  __/  >  <| |_) | |_
     \__,_(_) |_| |_| |_|\__,_|_|\_\___| /_/\_\ .__/ \__|
    ;                                         |_|


    * create a sas v5 export file containing 3 sas datasets;
    * if you do not have sas use the code below to download from github;

    options obs=30;
    LIBNAME sasxpt XPORT "d:/xpt/sasxpt.xpt";
    proc copy in=sashelp out=sasxpt;
      select class prdsale prdsal2;
    run;quit;

    *_            _                     _                 _              _
    | |__      __| | _____      ___ __ | | ___   __ _  __| | __  ___ __ | |_
    | '_ \    / _` |/ _ \ \ /\ / / '_ \| |/ _ \ / _` |/ _` | \ \/ / '_ \| __|
    | |_) |  | (_| | (_) \ V  V /| | | | | (_) | (_| | (_| |  >  <| |_) | |_
    |_.__(_)  \__,_|\___/ \_/\_/ |_| |_|_|\___/ \__,_|\__,_| /_/\_\ .__/ \__|
                                                                  |_|
    ;
    * If you cannot create the sas transport datasets using the sas
      code above you can use the R code below to download and decode code.
    * Note GitHub does not support binary downloads
      so I have created a base64 encoded version on the binary v5 transport file.
    * The encoded version at https://tinyurl.com/2cpwhja6 can be decoded into
      the binary v5 transport file sasxpt.xpt;

    * binary download of the v5 transport file R code;
    %utl_submit_r64('
    
    # remove output xpt file if it exists;
    url<-"https://tinyurl.com/2cpwhja6";
    xpt="d:/xpt/sasxpt.xpt";
    if (file.exists(xpt)) { file.remove(xpt) };
    # download base64 encoded version of the binary sas v5 transport file;
    tmp<-tempfile();
    download.file(url, tmp);
    # decode the encoded file back to sas v5 binary transport file;
    base64::decode(tmp, xpt);
    
    ');

    *                    _     _                    _
      ___    __  ___ __ | |_  | |__   ___  __ _  __| | ___ _ __
     / __|   \ \/ / '_ \| __| | '_ \ / _ \/ _` |/ _` |/ _ \ '__|
    | (__ _   >  <| |_) | |_  | | | |  __/ (_| | (_| |  __/ |
     \___(_) /_/\_\ .__/ \__| |_| |_|\___|\__,_|\__,_|\___|_|
                  |_|
    ;

    CONTETS OF HEADER;

        1. Header consists of 80 byte records that are back to back.
           Records are not separted by a cariage-return-line ie cr/lf '0D0A'x.
        2. It is a v5 transport because of first record
           'HEADER RECORD*******LIBRARY HEADER RECORD!!!!!!!000000000000000000000000000000'
        3. Created by 32 bit SAS on Win 10 because 'W32_10PR'
        4. Created on '27MAR21:08:41:06
        5. First dataset is class.sas7bdat
        6. Type 'data' created by SAS 9.4
           'CLASS   SASDATA 9.4'

    * Macro utlhex created this output. Macro on end and in  'https://tinyurl.com/y9nfugth'.

    %utlhex(uinflt =d:/xpt/sasxpt.xpt,uotflt =d:/xpt/sasxpt.txt);

    Input  d:/xpt/sasxpt.xpt
    output d:/xpt/sasxpt.txt


     --- Record Number ---  1   ---  Record Length ---- 80

    HEADER RECORD*******LIBRARY HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444545524444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAC92212908514520253F24111111100000000000000000000000000000000


     --- Record Number ---  2   ---  Record Length ---- 80

    SAS     SAS     SASLIB  9.4     W32_10PR                        27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    54522222545222225454442232322222533533552222222222222222222222223344533333333333
    3130000031300000313C92009E400000732F100200000000000000000000000027D1221A08A41A06


     --- Record Number ---  3   ---  Record Length ---- 80

    27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33445333333333332222222222222222222222222222222222222222222222222222222222222222
    27D1221A08A41A060000000000000000000000000000000000000000000000000000000000000000


     --- Record Number ---  4   ---  Record Length ---- 80

    HEADER RECORD*******MEMBER  HEADER RECORD!!!!!!!000000000000000001600000000140
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444445224444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAD5D252008514520253F24111111100000000000000000160000000014000


     --- Record Number ---  5   ---  Record Length ---- 80

    HEADER RECORD*******DSCRPTR HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222454555524444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAA433204208514520253F24111111100000000000000000000000000000000


     --- Record Number ---  6   ---  Record Length ---- 80

    SAS     CLASS   SASDATA 9.4     W32_10PR                        27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    54522222444552225454454232322222533533552222222222222222222222223344533333333333
    313000003C133000313414109E400000732F100200000000000000000000000027D1221A08A41A06

    *                                         _
      ___      ___ ___  _ __ _ __ _   _ _ __ | |_  __  ___ __ | |_
     / __|    / __/ _ \| '__| '__| | | | '_ \| __| \ \/ / '_ \| __|
    | (__ _  | (_| (_) | |  | |  | |_| | |_) | |_   >  <| |_) | |_
     \___(_)  \___\___/|_|  |_|   \__,_| .__/ \__| /_/\_\ .__/ \__|
                                       |_|              |_|
    ;

    * corrupted because non-binary upload or download;
    * import corrupted v5 transport;

    %utl_submit_r64('
    # remove output xpt file if it exists;
    url<-"https://tinyurl.com/37b9pn29";
    xpt="d:/xpt/sasv5corrupt.xpt";
    if (file.exists(xpt)) { file.remove(xpt) };
    # download base64 encoded version of the binary sas v5 transport file;
    tmp<-tempfile();
    download.file(url, tmp);
    # decode the encoded file back to sas v5 binary transport file;
    base64::decode(tmp, xpt);
    ');

    * Try to import v5 xpt into R

    %utl_submit_r64('
     library(SASxport);
     lookup.xport("d:/xpt/sasv5corrupt.xpt");
    ');

    * PROBLEM WITH HEADER;

    Stderr output:
    Error in lookup.xport.inner(file) :
      SAS transfer file has incorrect library header
    Calls: lookup.xport -> lookup.xport.inner

    * delete output text file if it exists;
    %utl_submit_r64('
    # remove output hext dump of corrupt v5 xpt file if it exists;
    xpt="d:/xpt/sasv5corrupt.txt";
    if (file.exists(xpt)) { file.remove(xpt) };
    ');

    * Macro utlhex created this output. Macro on end and in  'https://tinyurl.com/y9nfugth';.
    %utlhex(uinflt =d:/xpt/sasv5corrupt.xpt,uotflt =d:/xpt/sasv5corrupt.txt);


     Issues with the corrupt v5 xport file

      1. The header records are not on 80 byte boundaries.

         Record 4 is offset by 6 bytes because three CR/LFs have been inserted.

         --- Record Number ---  4   ---  Record Length ---- 80
             ..HEADER RECORD*******MEMBER  HEADER RECORD!!!!!!!00000000000000000160000000
         1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8

      2. We have CR/LFa '0D0A'x in the data. Where they should not be.

          --- Record Number ---  2 hav 'OD0A'x in bytes 1 & 2
         ..
         1.
         00
         DA


    d:/xpt/sasv5corrupt.xpt
    d:/xpt/sasv5corrupt.txt


     --- Record Number ---  1   ---  Record Length ---- 80

    HEADER RECORD*******LIBRARY HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444545524444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAC92212908514520253F24111111100000000000000000000000000000000


     --- Record Number ---  2   ---  Record Length ---- 80

    ..SAS     SAS     SASLIB  9.4     W32_10PR                        27MAR21:08:41:
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    00545222225452222254544422323222225335335522222222222222222222222233445333333333
    DA3130000031300000313C92009E400000732F100200000000000000000000000027D1221A08A41A


     --- Record Number ---  3   ---  Record Length ---- 80

    06..27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33003344533333333333222222222222222222222222222222222222222222222222222222222222
    06DA27D1221A08A41A06000000000000000000000000000000000000000000000000000000000000


     --- Record Number ---  4   ---  Record Length ---- 80

        ..HEADER RECORD*******MEMBER  HEADER RECORD!!!!!!!00000000000000000160000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    22220044444525444542222222444445224444452544454222222233333333333333333333333333
    0000DA8514520253F24AAAAAAAD5D252008514520253F24111111100000000000000000160000000


     --- Record Number ---  5   ---  Record Length ---- 80

    0140  ..HEADER RECORD*******DSCRPTR HEADER RECORD!!!!!!!000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33332200444445254445422222224545555244444525444542222222333333333333333333333333
    014000DA8514520253F24AAAAAAA433204208514520253F241111111000000000000000000000000

    *         _                            _                _
      ___    (_)_ __ ___  _ __   ___  _ __| |_  __  ___ __ | |_
     / _ \   | | '_ ` _ \| '_ \ / _ \| '__| __| \ \/ / '_ \| __|
    |  __/_  | | | | | | | |_) | (_) | |  | |_   >  <| |_) | |_
     \___(_) |_|_| |_| |_| .__/ \___/|_|   \__| /_/\_\ .__/ \__|
                         |_|                         |_|
    ;

     Process to import three SAS datasets

          1. Use lookup functions to get the names on SAS datasets inside the v5 transport
          2. Call read.xport to create a Rlist of dataframes corresponding to the sas datasets
          3. Delete the output datasets if they exist
          4. Write the three tables using the free evaluation copy of DULLES Research S-JDBC
          5. The d=code produces dataframeas class, prdsale and prdsal2 and corresponding sas datasets
          6. use the macro utl_optlen to optimize lengths (note the 255 byte fields)


    # could not get one of the apply functions or a loop to work with dbWriteTable;

    * see https://cran.r-project.org/web/packages/SASxport/SASxport.pdf

    %utl_submit_r64(%tslit(
    library(RJDBC);
    library(haven);
    library(SASxport);
    xpt="d:/xpt/sasxpt.xpt";
    lookup.xport(xpt);
    mta<-lookup.xport(xpt);
    mtanam<-names(mta);
    rlst<-read.xport(xpt,keep=mtanam);
    for (i in 1:length(mtanam) ) {
         outfyl<-paste("d:/sd1/",tolower(mtanam[i]),".sas7bdat",sep="");
         if (file.exists(outfyl)) { file.remove(outfyl) };
        };
    class<-rlst[[1]];
    prdsale<-rlst[[2]];
    prdsal2<-rlst[[3]];
    drv<- JDBC("com.dullesopen.jdbc.Driver","d:/carolina/carolina-jdbc-2.4.3.jar");
    conn <- dbConnect(drv, "jdbc:carolina:bulk:libnames=(dir='d:/sd1')", "", "");
    rc<-dbWriteTable(conn,"class",class);
    rc<-dbWriteTable(conn,"prdsale",prdsale);
    rc<-dbWriteTable(conn,"prdsal2",prdsal2);
    ));


    SAS xport file (output of lookup function)
    -------------------------------------------
    Filename: `\\E6420\d\xpt\sasxpt.xpt'

    Variables in data set `CLASS':
     dataset   name      type format flength fdigits iformat iflength ifdigits
       CLASS   NAME character              0       0                0        0
       CLASS    SEX character              0       0                0        0
       CLASS    AGE   numeric              0       0                0        0
       CLASS HEIGHT   numeric              0       0                0        0
       CLASS WEIGHT   numeric              0       0                0        0

    Variables in data set `PRDSALE':
     dataset     name      type  format flength fdigits iformat iflength ifdigits
     PRDSALE   ACTUAL   numeric  DOLLAR      12       2                0        0
     PRDSALE  PREDICT   numeric  DOLLAR      12       2                0        0
     PRDSALE  COUNTRY character   $CHAR      10       0                0        0
     PRDSALE   REGION character   $CHAR      10       0                0        0
     PRDSALE DIVISION character   $CHAR      10       0                0        0
     PRDSALE PRODTYPE character   $CHAR      10       0                0        0
     PRDSALE  PRODUCT character   $CHAR      10       0                0        0
     PRDSALE  QUARTER   numeric               8       0                0        0
     PRDSALE     YEAR   numeric               4       0                0        0
     PRDSALE    MONTH   numeric MONNAME       3       0                0        0

    Variables in data set `PRDSAL2':
     dataset     name      type  format flength fdigits iformat iflength ifdigits
     PRDSAL2  COUNTRY character   $CHAR      10       0                0        0
     PRDSAL2    STATE character   $CHAR      22       0                0        0
     PRDSAL2   COUNTY character   $CHAR      20       0                0        0
     PRDSAL2   ACTUAL   numeric  DOLLAR      12       2                0        0
     PRDSAL2  PREDICT   numeric  DOLLAR      12       2                0        0
     PRDSAL2 PRODTYPE character   $CHAR      10       0                0        0
     PRDSAL2  PRODUCT character   $CHAR      10       0                0        0
     PRDSAL2     YEAR   numeric               4       0                0        0
     PRDSAL2  QUARTER   numeric               8       0                0        0
     PRDSAL2    MONTH   numeric MONNAME       3       0                0        0
     PRDSAL2    MONYR   numeric   MONYY       0       0   MONYY        0        0

    *     _               _
      ___| |__   ___  ___| | __
     / __| '_ \ / _ \/ __| |/ /
    | (__| | | |  __/ (__|   <
     \___|_| |_|\___|\___|_|\_\

    ;

    * RUN OPTLEN macro;

    libname sd1 "d:/sd1";

    Contents of sd1.class

     Variables in Creation Order

    #    Variable    Type    Len

    1    NAME        Char    255
    2    SEX         Char    255
    3    AGE         Num       8
    4    HEIGHT      Num       8
    5    WEIGHT      Num       8

    * to fix the long charcater variables run this macro;

    %utl_optlen(inp=sd1.class,out=sd1.class);

    Contents of sd1.class

     Variables in Creation Order

    #    Variable    Type    Len

    1    NAME        Char      7
    2    SEX         Char      1
    3    AGE         Num       3
    4    HEIGHT      Num       8
    5    WEIGHT      Num       3

    * __                    _                            _
     / _|   ___  __ _ ___  (_)_ __ ___  _ __   ___  _ __| |_
    | |_   / __|/ _` / __| | | '_ ` _ \| '_ \ / _ \| '__| __|
    |  _|  \__ \ (_| \__ \ | | | | | | | |_) | (_) | |  | |_
    |_|(_) |___/\__,_|___/ |_|_| |_| |_| .__/ \___/|_|   \__|
                                       |_|
    ;

    libname xpt xport "d:/xpt/sasxpt.xpt";
    proc copy in=xpt out=work;
    run;quit;
    proc contents lib=work mt=data;
    run;quit;

               Member  Obs, Entries
      Name     Type     or Indexes   Vars  Label

      CLASS    DATA         19         5
      PRDSAL2  DATA         30        11   Furniture sales data
      PRDSALE  DATA         30        10   Furniture sales data

    *____                            _     _____ ____  ____
    |___ \      ___ _ __   ___  _ __| |_  |_   _| __ )|  _ \
      __) |    / __| '_ \ / _ \| '__| __|   | | |  _ \| | | |
     / __/ _  | (__| |_) | (_) | |  | |_    | | | |_) | |_| |
    |_____(_)  \___| .__/ \___/|_|   \__|   |_| |____/|____/
                   |_|
    ;
