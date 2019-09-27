# utl_memlist-macro-creating-a-list-of-views-in-sashelp-that-begin-with-vs
    SAS-L: Memlist macro creating a list of views in sashelp that begin with vs;                                        
                                                                                                                        
    Problem                                                                                                             
        List Views in sashelp that begin with VS                                                                        
                                                                                                                        
    github                                                                                                              
    https://tinyurl.com/yxhseqc4                                                                                        
    https://github.com/rogerjdeangelis/utl_memlist-macro-creating-a-list-of-views-in-sashelp-that-begin-with-vs         
                                                                                                                        
                                                                                                                        
    macros                                                                                                              
    https://tinyurl.com/y9nfugth                                                                                        
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                          
                                                                                                                        
                                                                                                                        
    *_                   _                                                                                              
    (_)_ __  _ __  _   _| |_                                                                                            
    | | '_ \| '_ \| | | | __|                                                                                           
    | | | | | |_) | |_| | |_                                                                                            
    |_|_| |_| .__/ \__,_|\__|                                                                                           
            |_|                                                                                                         
    ;                                                                                                                   
                                                                                                                        
     sashelp library                                                                                                    
                                                                                                                        
    *                                                                                                                   
     _ __  _ __ ___   ___ ___  ___ ___                                                                                  
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|                                                                                 
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                                 
    | .__/|_|  \___/ \___\___||___/___/                                                                                 
    |_|                                                                                                                 
    ;                                                                                                                   
                                                                                                                        
    Views in sashelp that begin with VS                                                                                 
                                                                                                                        
    %put %utl_memlist(sashelp,prx=/^VS/i,memtype=view);                                                                 
                                                                                                                        
                                                                                                                        
    *            _               _                                                                                      
      ___  _   _| |_ _ __  _   _| |_                                                                                    
     / _ \| | | | __| '_ \| | | | __|                                                                                   
    | (_) | |_| | |_| |_) | |_| | |_                                                                                    
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                   
                    |_|                                                                                                 
    ;                                                                                                                   
                                                                                                                        
    VSACCES VSCATLG VSLIB VSTABLE VSTABVW VSTYLE VSVIEW                                                                 
                                                                                                                        
                                                                                                                        
    *                                                                                                                   
     _ __ ___   __ _  ___ _ __ ___                                                                                      
    | '_ ` _ \ / _` |/ __| '__/ _ \                                                                                     
    | | | | | | (_| | (__| | | (_) |                                                                                    
    |_| |_| |_|\__,_|\___|_|  \___/                                                                                     
                                                                                                                        
    ;                                                                                                                   
                                                                                                                        
                                                                                                                        
    /******************************************************************************************/                        
    /*                                                                                        */                        
    /*         Name: utlmemList.sas                                                           */                        
    /*         Type: Macro function                                                           */                        
    /*                                                                                        */                        
    /*   Description: Returns list of tables/views in a library                               */                        
    /*                                                                                        */                        
    /*   Parameters:                                                                          */                        
    /*           Lib           - libname  ie work                                             */                        
    /*           quotecomma    - double quote table names separated by commas:                */                        
    /*           prx=          - PRX expression                                               */                        
    /*                                                                                        */                        
    /*   Examples:                                                                            */                        
    /*           proc datasets lib=work kill;run;quit;                                        */                        
    /*           data one two tre;                                                            */                        
    /*             set sashelp.stocks(keep=ADJCLOSE CLOSE DATE obs=10);                       */                        
    /*           run;quit;                                                                    */                        
    /*                                                                                        */                        
    /*           %put %utl_memlist(work);                            ONE TRE TWO              */                        
    /*           %put %utl_memlist(sashelp,prx=/class/i);            CLASS CLASSFIT           */                        
    /*           %put %utl_memlist(work,quotecomma=Y);               "ONE","TRE","TWO"        */                        
    /*           %put %utl_memlist(work,prx=/one/i);                 ONE                      */                        
    /*           %put %utl_memlist(work,quotecomma=Y,prx=/one/i);    "ONE"                    */                        
    /*           %put %utl_memlist(sashelp,prx=/^VS/i,memtype=view); VSACCES VSCATLG VSLIB    */                        
    /*                                                                                        */                        
    /*  Inspired by                                                                           */                        
    /*                                                                                        */                        
    /*  Søren Lassen, s.lassen@post.tele.dk                                                   */                        
    /*                                                                                        */                        
    /*                                                                                        */                        
    /******************************************************************************************/                        
                                                                                                                        
    %macro utl_memlist(lib,quotecomma=0,prx=0,memtype=data);                                                            
                                                                                                                        
      /* %let lib=work; %let quotecomma=0; %let prx=/T/;  */                                                            
      /* %let lib=work; %let quotecomma=0; %let prx=0;  %let memtype=cat; */                                            
                                                                                                                        
       %symdel lst whr /nowarn;                                                                                         
       %local lst whr;                                                                                                  
                                                                                                                        
       %let rc=%sysfunc(dosubl('                                                                                        
          proc contents data=&lib.._all_ mt=&memtype directory                                                          
               out=__dir(keep=memname varnum where=(varnum=1)) noprint nodetails;                                       
          run;quit;                                                                                                     
          %let whr=%str(where=(1=1));                                                                                   
          %if "&prx" ne "0" %then %do;                                                                                  
              %let whr=%str(where=(prxmatch("&prx",memname)>0));                                                        
              %put &=whr;                                                                                               
          %end;                                                                                                         
          data _null_;                                                                                                  
             length lst $4096;                                                                                          
             retain lst;                                                                                                
             set __dir(&whr) end=dne;                                                                                   
             if "&quotecomma" ne "0" then do;                                                                           
                lst=catx(",",lst,quote(strip(memname)));                                                                
             end;                                                                                                       
             else do;                                                                                                   
                lst=catx(" ",lst,memname);                                                                              
             end;                                                                                                       
                                                                                                                        
             if dne then call symputx("lst",lst);                                                                       
          run;quit;                                                                                                     
          %put &=lst;                                                                                                   
      '));                                                                                                              
       &lst;                                                                                                            
                                                                                                                        
    %mend utl_memlist;                                                                                                  
                                                                                                                        
    /*                                                                                                                  
    proc datasets lib=work kill;run;quit;                                                                               
                                                                                                                        
    data one two tre;                                                                                                   
       set sashelp.stocks(keep=ADJCLOSE CLOSE DATE obs=10);                                                             
    run;quit;                                                                                                           
                                                                                                                        
    %put %utl_memlist(sashelp,prx=/^VS/i,memtype=view);                                                                 
    %put %utl_memlist(work);                                                                                            
    %put %utl_memlist(work,quotecomma=Y);                                                                               
    %put %utl_memlist(work,prx=/one/i);                                                                                 
    %put %utl_memlist(work,quotecomma=Y,prx=/one/i);                                                                    
    */                                                                                                                  
                                                                                                                        
                                                                                                                        
