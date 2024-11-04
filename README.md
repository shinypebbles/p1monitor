# p1monitor
Get data from p1monitor into Oracle
'''
create or replace PROCEDURE getp1monitor AS
  req   UTL_HTTP.REQ;
  resp  UTL_HTTP.RESP;
  value VARCHAR2(32024);
  json_arr JSON_ARRAY_T;
  i INTEGER;
  record JSON_ARRAY_T;
  p0 date;
  p1 number;
  p2 number;
  p3 number;
  p4 number;
  p5 number;
  p6 number;
  p7 number;
  p8 number;
  p9 number;
BEGIN
  req := UTL_HTTP.BEGIN_REQUEST('http://p1monitor/api/v1/powergas/year');
  UTL_HTTP.SET_HEADER(req, 'User-Agent', 'Mozilla/4.0');
  resp := UTL_HTTP.GET_RESPONSE(req);
  LOOP
    UTL_HTTP.READ_LINE(resp, value, TRUE);
    DBMS_OUTPUT.PUT_LINE('value='||value);
    json_arr := JSON_ARRAY_T.parse(value);
    FOR i IN 0 .. json_arr.get_size - 1 LOOP
      record:=JSON_ARRAY_T.parse(json_arr.get(i).stringify);
      DBMS_OUTPUT.PUT_LINE( record.get_string(0)||'^'|| record.get_number(1)||'^'|| record.get_number(2)||'^'|| record.get_number(3)||'^'|| record.get_number(4)||'^'|| record.get_number(5)||'^'|| record.get_number(6)||'^'|| record.get_number(7)||'^'|| record.get_string(8)||'^'|| record.get_number(9)||'^'|| record.get_number(10)||'^'|| record.get_number(11) ); 
      DBMS_OUTPUT.PUT_LINE('i='||i||' '||json_arr.get(i).stringify);
      p0:=to_date(record.get_string(0),'YYYY-MM-DD HH24:MI:SS');
      p1:=record.get_number(1);
      p2:=record.get_number(2);
      p3:=record.get_number(3);
      p4:=record.get_number(4);
      p5:=record.get_number(5);
      p6:=record.get_number(6);
      p7:=record.get_number(7);
      p8:=record.get_number(8);
      p9:=record.get_number(9);
      INSERT INTO p1_year (
        TIMESTAMP_LOCAL,
        TIMESTAMP_UTC,
        CONSUMPTION_KWH_LOW,
        CONSUMPTION_KWH_HIGH,
        PRODUCTION_KWH_LOW,
        PRODUCTION_KWH_HIGH,
        CONSUMPTION_DELTA_KWH,
        PRODUCTION_DELTA_KWH,
        CONSUMPTION_GAS_M3,
        CONSUMPTION_GAS_DELTA_M3
      ) VALUES (
      p0,
      p1,
      p2,
      p3,
      p4,
      p5,
      p6,
      p7,
      p8,
      p9
      );
      
    END LOOP;

  END LOOP;
  UTL_HTTP.END_RESPONSE(resp);
EXCEPTION
  WHEN UTL_HTTP.END_OF_BODY THEN
    UTL_HTTP.END_RESPONSE(resp);
  --WHEN others THEN
    --DBMS_OUTPUT.PUT_LINE('other error');
END;
/
'''
