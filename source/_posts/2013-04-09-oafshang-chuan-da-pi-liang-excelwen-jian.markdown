---
layout: post
title: "OAF上传大批量EXCEL文件"
date: 2013-04-09 10:46
comments: true
categories: OAF
tags: 
- OAF
- POI
- Bulk Insert
- 导入EXCEL
---
客户有个需求：大量数据通过EXCEL导入系统表，并能提供效验及去重功能。

####设计思路####


- 采用POI解析EXCEL文件；
- bulk insert 插入数据到临时表
- 临时表结合正式表去除重复

####POI解析EXCEL####

在CO的`processFormRequest`方法中添加如下:    
{% codeblock lang:java %}
  public void processFormRequest(OAPageContext pageContext, OAWebBean webBean)
  {
    super.processFormRequest(pageContext, webBean);
    OAApplicationModule am = pageContext.getApplicationModule(webBean);

    if (pageContext.getParameter("uploadBtn") != null)
    {
      DataObject fileUploadData = (DataObject)pageContext.getNamedDataObject("uploadFile");
      String fileName = null;
      try {
        fileName = (String)fileUploadData.selectValue(null, "UPLOAD_FILE_NAME");
      } catch(NullPointerException ex)
      {
        throw new OAException("请先选择需要上传的文件", OAException.INFORMATION);
      }

      if(fileName.endsWith(".xlsx"))
      {
                throw new OAException("不支持高版本EXCEL文件,请转换成2003版本的EXCEL再上传",OAException.WARNING);
      }else if(!fileName.endsWith(".xls"))
      {
                throw new OAException("请上传EXCEL类型文件",OAException.ERROR);
      }

      BlobDomain uploadedByteStream = (BlobDomain)fileUploadData.selectValue(null, fileName);
      Serializable aserializable2[] = {uploadedByteStream};
      Class aclass2[] = {BlobDomain.class };
      am.invokeMethod("importFile", aserializable2,aclass2);   
    }
  }
{% endcodeblock %}


####在AM中编写importFile方法

{% codeblock lang:java %}
  public void importFile(BlobDomain fileData) 
  {
 	InputStream inStr = fileData.getInputStream();
    HSSFWorkbook workbook=null;
    try{
     workbook= new HSSFWorkbook(inStr);
    }catch(java.io.IOException e)
    {
      throw new OAException("读取上传的文件出错,请检查上传的文件是否为97-2003版本的EXCEL可读文件.",OAException.ERROR);
    }
    //信号1
    if(workbook==null)
    {
      throw new OAException("ERROR001:workbook为空,请重新上传文件.",OAException.ERROR);
    }
    HSSFSheet sheet1 = workbook.getSheet("1");
    if(sheet1==null)
    {
      throw new OAException("ERROR002:sheet1为空,上传文件中找不到名字为'1'的工作表,请检查工作表命名是否符合规则.",OAException.ERROR);
    }
    int rowCount1 = sheet1.getLastRowNum();
    int realCount1=0;
    
    Object[] o_vin1=new Object[rowCount1],
    o_type1=new Object[rowCount1],
    o_color1=new Object[rowCount1],
    o_date1=new Object[rowCount1],
    o_des1=new Object[rowCount1];
    String vin1=null,type1=null,color1=null;
    java.util.Date vdate1=null;
    for (int r1=1; r1 <= rowCount1; r1++)
    {
      HSSFRow tempRow1=sheet1.getRow(r1);
      if(tempRow1==null)
      {
        throw new OAException("INFO:sheet[1]中第"+(r1+1)+"行数据异常,请检查上传文件",OAException.INFORMATION);
      }
      if(tempRow1.getCell((short)0)==null||tempRow1.getCell((short)0).getStringCellValue()==null||"".equals(tempRow1.getCell((short)0).getStringCellValue().trim()))
      {
        throw new OAException("INFO:sheet[1]中第"+(r1+1)+"行第1列数据异常,请检查上传文件",OAException.INFORMATION);
      }
      
      if(tempRow1.getCell((short)1)==null||tempRow1.getCell((short)1).getStringCellValue()==null)
      {
        throw new OAException("INFO:sheet[1]中第"+(r1+1)+"行第2列数据异常,请检查上传文件",OAException.INFORMATION);
      }
      
      if(tempRow1.getCell((short)2)==null||tempRow1.getCell((short)2).getStringCellValue()==null)
      {
        throw new OAException("INFO:sheet[1]中第"+(r1+1)+"行第3列数据异常,请检查上传文件",OAException.INFORMATION);
      }
      if(tempRow1.getCell((short)3)==null||tempRow1.getCell((short)3).getDateCellValue()==null)
      {
        throw new OAException("INFO:sheet[1]中第"+(r1+1)+"行第4列数据异常,请检查上传文件",OAException.INFORMATION);
      }
      vin1=tempRow1.getCell((short)0).getStringCellValue().trim();
      type1=tempRow1.getCell((short)1).getStringCellValue().trim();
      color1=tempRow1.getCell((short)2).getStringCellValue().trim();
      vdate1=tempRow1.getCell((short)3).getDateCellValue();    

      o_vin1[r1-1]=vin1;
      o_type1[r1-1]=type1;
      o_color1[r1-1]=color1;
      o_date1[r1-1]=new oracle.jbo.domain.Date(new java.sql.Date(vdate1.getTime()));
      o_des1[r1-1]=null;
      realCount1++;      
    }

    List exceptions = new ArrayList();
    exceptions.add(new OAException("解析出上传EXCEL文件中信号1表"+realCount1+"条记录",OAException.INFORMATION ));
    //bulkinsert...
    exceptions.add(new OAException("开始插入信号1临时表",OAException.INFORMATION ));    
    String result=this.bulkInsert1(o_vin1,o_type1,o_color1,o_date1,o_des1);
    exceptions.add(new OAException("结果:"+result,OAException.INFORMATION ));        
    exceptions.add(new OAException("结束插入信号1临时表",OAException.INFORMATION ));      

	OAException.raiseBundledOAException(exceptions);
}
{% endcodeblock %}

#### OAF调用bulk insert插入大批量数据####

{% codeblock lang:java %}
 public String bulkInsert1(Object[] vin,Object[] type,Object[] color,Object[] vdate, Object[] des) 
    {
        CallableStatement st = null;
        String result="";
        String stmt = "{ call ch_report_pkg.INSERTREPORT1(?,?,?,?,?,?) }";
        try {
            st=this.getDBTransaction().createCallableStatement(stmt,0);
            ArrayDescriptor T_VARCHAR30=null,
             T_VARCHAR10=null,
             T_VARCHAR200=null,
             T_DATE=null;
            ARRAY a_vin=null,a_type=null,a_color=null,a_vdate=null,a_des=null;
            T_VARCHAR30=oracle.sql.ArrayDescriptor.createDescriptor("T_VARCHAR30",st.getConnection());
            T_VARCHAR10=oracle.sql.ArrayDescriptor.createDescriptor("T_VARCHAR10",st.getConnection());
            T_VARCHAR200=oracle.sql.ArrayDescriptor.createDescriptor("T_VARCHAR200",st.getConnection());
            T_DATE=oracle.sql.ArrayDescriptor.createDescriptor("T_DATE",st.getConnection());
            a_vin=new ARRAY(T_VARCHAR30,st.getConnection(),vin);
            a_type=new ARRAY(T_VARCHAR30,st.getConnection(),type);
            a_color=new ARRAY(T_VARCHAR10,st.getConnection(),color);
            a_vdate=new ARRAY(T_DATE,st.getConnection(),vdate);            
            a_des=new ARRAY(T_VARCHAR200,st.getConnection(),des);              
            st.setArray(1,a_vin);
            st.setArray(2,a_type);
            st.setArray(3,a_color);
            st.setArray(4,a_vdate);
            st.setArray(5,a_des);
            st.registerOutParameter(6, OracleTypes.VARCHAR);
            st.execute();  
            result=st.getString(6);
            return result;
        } catch (Exception e) {
            throw new JboException(e);
        } finally {          
            if (st != null) {
                try {
                    st.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }        
    }

{% endcodeblock %}

将POI解析出来的数据，包装成Object对象作为参数传递给数据库ch_report_pkg包中的INSERTREPORT1方法，bulk insert到数据库，极大地提高了插入数据效率。
{% codeblock lang:sql %}
PROCEDURE INSERTREPORT1(I_VIN      IN T_VARCHAR30,
                          I_CAR_TYPE IN T_VARCHAR30,
                          I_COLOR    IN T_VARCHAR10,
                          I_VDATE    IN T_DATE,
                          I_DES      IN T_VARCHAR200,
                          MESSAGE    OUT VARCHAR2) IS
    I INTEGER;
  BEGIN
    DELETE FROM CH_REPORT_1_TEMP;
    COMMIT;
    FORALL I IN 1 .. I_VIN.COUNT
      INSERT INTO CH_REPORT_1_TEMP
        (VIN, CAR_TYPE, COLOR, VDATE, DESCRIPTION)
      VALUES
        (I_VIN(I), I_CAR_TYPE(I), I_COLOR(I), I_VDATE(I), I_DES(I));
    MESSAGE := I_VIN.COUNT || '条数据正常插入!';
    COMMIT;
  EXCEPTION
    WHEN OTHERS THEN
      MESSAGE := '出现异常，错误代码如下：' || SQLERRM;
      ROLLBACK;
  END;
{% endcodeblock %}

此外，需要先根据表结构定义出字段类型如下：
{% codeblock lang:sql %}
create or replace TYPE T_VARCHAR30 IS TABLE OF VARCHAR2(30);
create or replace TYPE T_VARCHAR10 IS TABLE OF VARCHAR2(10);
create or replace TYPE T_VARCHAR200 IS TABLE OF VARCHAR2(200);
create or replace TYPE T_DATE IS TABLE OF DATE;
{% endcodeblock %}

那么数据就顺利批量插入到临时表中了。

####数据去除重复####

需要根据插入数据的主键VIN判断是否跟数据库中存在的数据重复，    
在程序中对比的话效率很低，所以采用在数据库中通过语句    
`select count(vin) from (table union temp_table) group by vin`    
来判断，count数量大于1表示有重复。
