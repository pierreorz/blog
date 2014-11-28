---
layout: post
title: "使用RIDC在webcenter portal集成ECM的文档查询功能"
date: 2014-11-28 17:17
comments: true
categories: webcenter
tags: 
- webcenter
- RIDC
---
#### <i class="icon-file"></i>   需求：

标准的document service很不灵活，实际开发中少不了要开发定制。那么问题来了，通过RIDC很灵活的查询出了需要的文档，如何集成到portal上去呢

#### <i class="icon-folder-open"></i> 分析：

使用RIDC查询出需要的文档，存放在POJO等数据模型中
利用POJO生成dataControl，开发taskflow等UI层
将以上功能开发成为extend.spaces.webapp，扩展到webcenter spaces
以下主要展现第一步：通过RIDC查询及POJO生成数据模型。

#### <i class="icon-pencil"></i> 技术实现：

- 使用RIDC查询文档

以下是主要查询方法, query传入的即是ECM的queryText，例如：
{% codeblock lang:java %}
dDocType <matches> `PJT-INTERFACE-DOC`
{% endcodeblock %}
通过拼接好需要的查询条件以及用户便可以查询出文档。
{% codeblock lang:java %}
	    public List<Document> search(String username, String query) {
        ServiceResponse serviceResponse = null;
        List<Document> list = new ArrayList<Document>();

        try {
            if (query == null) {
                return list;
            }
            IdcClient client = getIdcClient();
            DataBinder dataBinderReq = client.createBinder();
            dataBinderReq.putLocal("IdcService", "GET_SEARCH_RESULTS");
            dataBinderReq.putLocal("QueryText", query.toString());
            dataBinderReq.putLocal("ResultCount", "100");
            serviceResponse =
                    client.sendRequest(new IdcContext(username), dataBinderReq);
            DataBinder dataBinderRes = serviceResponse.getResponseAsBinder();
            DataResultSet resultSet =
                dataBinderRes.getResultSet("SearchResults");
            for (DataObject dataObject : resultSet.getRows()) {
                Document d = new Document();
                d.setContentId(dataObject.get("dDocName"));
                d.setDocumentId(dataObject.get("dID"));
                d.setOwner(dataObject.get("dDocAuthor"));
                d.setTitle(dataObject.get("dDocTitle"));
                d.setDocType(dataObject.get("dDocType"));
                d.setXspeciality(dataObject.get("xSPECIALTY"));
                d.setXrcvMajor(dataObject.get("xRCV_MAJOR"));
                Date date = dataObject.getDate("dInDate");
                SimpleDateFormat sf = new SimpleDateFormat("yyyy/mm/dd");
                String format = sf.format(date);
                d.setDindate(format);
                list.add(d);
            }
        } catch (Exception ex) {
            System.out.println("Error Search: " + ex.getMessage());
        } finally {
            if (serviceResponse != null) {
                serviceResponse.close();
            }
        }
        return list;
    } //
{% endcodeblock %}
其中Document.java即是POJO类
{% codeblock lang:java %}
public class Document {
    private String contentId;
    private String documentId;
    private String owner;
    private String title;
    private String doctype;
    private String securitygroup;
    private String dindate;
    private String xDesignPhase;
    private String xspeciality;//send
    private String xrcvMajor;//receive
	   ......get set accessor......
}//end class
{% endcodeblock %}

创建facade类：MyCondition.java，主要是调用search方法接受RIDC查询的结果集，生成dataControl供UI层使用。参考代码如下：
{% codeblock lang:java %}
public class MyCondition {
    private static final ADFLogger LOGGER = ADFLogger.createADFLogger(MyCondition.class);
    private ArrayList<Document> receiveDocumentList;
    private ArrayList<Document> sendDocumentList;
    public MyCondition() {
        this.receiveDocumentList=new ArrayList<Document>();
        this.sendDocumentList=new ArrayList<Document>();
    }
    /*******
     * 初始化，调用RIDC API查询出文档数据集
     * *****/
    public String init(){
        LOGGER.severe("start init....");
        Object userId = JSFUtils.resolveExpression("#{securityContext.userName}");
        Object projectCode = JSFUtils.resolveExpression("#{pageFlowScope.inputProjectCode}");
        List<String> spcCode = (List<String>)(JSFUtils.resolveExpression("#{pageFlowScope.spcCode}")==null?null:JSFUtils.resolveExpression("#{pageFlowScope.spcCode}"));
        if(userId==null||"".equals(userId)){
            LOGGER.severe("userId is null ");
            FacesMessage msg = new FacesMessage("用户为空请重试!");
            JSFUtils.getFacesContext().addMessage(null, msg);
            return "NO";
        }
        if(projectCode==null||"".equals(projectCode)){
            LOGGER.severe("projectCode is null ");
            FacesMessage msg = new FacesMessage("项目ID为空请重试!");
            JSFUtils.getFacesContext().addMessage(null, msg);
            return "NO";
        }
        /******
         * 
         * 生成发出专业SQL,生成接受专业SQL
         * 
         * ****/
        String sendSql=" xSPECIALTY <matches> `NULL` ";
        String receiveSql=" xRCV_MAJOR <matches> `NULL` ";
        if(spcCode==null||spcCode.size()==0){
            LOGGER.severe("spcCode is null or size is 0");
            return "NO";
        }
        for(int i=0;i<spcCode.size();i++){
            sendSql=sendSql+" <OR> "+"xSPECIALTY <matches> `"+spcCode.get(i)+"` ";
            receiveSql=receiveSql+" <OR> "+"xRCV_MAJOR <matches> `"+spcCode.get(i)+"` ";
        }
        sendSql="("+sendSql+") <AND> dDocType <matches> `PJT-INTERFACE-DOC` <AND> xPROJECT_NUM <starts> `"+projectCode+"`";
        receiveSql="("+receiveSql+") <AND> dDocType <matches> `PJT-INTERFACE-DOC` <AND> xPROJECT_NUM <starts> `"+projectCode+"`";
        LOGGER.severe("sendSql:"+sendSql);
        LOGGER.severe("receiveSql:"+receiveSql);
        /*** 
         * 查询当前用户在该项目中的专业（ME,EL等)，拼接QueryText
         * dDocType <matches> `PJT-INTERFACE-DOC`
         * ******/
        UCMAdapter ap=new UCMAdapter();
        this.sendDocumentList = (ArrayList<Document>)ap.search(userId.toString(), sendSql);
        this.receiveDocumentList = (ArrayList<Document>)ap.search(userId.toString(), receiveSql);
        LOGGER.severe("success init...YES.");
        return "YES";
    }
    
    public void setReceiveDocumentList(ArrayList<Document> receiveDocumentList) {
        this.receiveDocumentList = receiveDocumentList;
    }

    public ArrayList<Document> getReceiveDocumentList() {
        return receiveDocumentList;
    }

    public void setSendDocumentList(ArrayList<Document> sendDocumentList) {
        this.sendDocumentList = sendDocumentList;
    }

    public ArrayList<Document> getSendDocumentList() {
        return sendDocumentList;
    }
}
{% endcodeblock %}
