---
layout: page
title: SOAPService
---

**Question:** How do I SEND/RECEIVE a SOAP message via Cocoa, to a WSDL server that normally expects  Java I/O?

**Background:** I'm given some Java code that sends/receives a SOAP envelope (XML data) to a server:

** Goal: **To *mimic*  the Java version in ObjC: exchange SOAP envelope (XML data) between ObjC client and a Server (black box).

**Java Code:**
    
    ...
    private String SOAP_SERVER_URL = "http://myconnect.myglobal.com/soap/sync";   // <-- target
    private final String SOAP_ACTION = "http://myconnn.myconnect.com/2007/02";  // <-- action
    ...

    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 1) Function soapReqHeader : Returns the SOAP header string    
    private String soapReqHeader() {
        String header = ""; 
        header = "<?xml version=\"1.0\" encoding=\"UTF-8\" ?>\r\n"
        + "<tns:Envelope xmlns:tns=\"http://schemas.xmlsoap.org/soap/envelope/\" xmlns:ns1=\"http://premconn.premiereconnect.com/2007/02\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" >\r\n"
        + "<tns:Header>\r\n"
        + "<ns1:Request tns:actor=\"http://schemas.xmlsoap.org/soap/actor/next\" tns:mustUnderstand=\"0\">\r\n"
        + "<ns1:ReceiverKey>http://xoa.xpedite.com/soap/sync</ns1:ReceiverKey>\r\n";
        return header;      
    } // end soapReqHeader()
    
    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 2) Function soapAuthenticate : Returns the SOAP authentication string    
    private String soapAuthenticate() {
        String authstr = "<ns1:Authentication>\r\n"
        + "<ns1:XDDSAuth>\r\n"
        + "<ns1:RequesterID>M2F" + " " + userName + "</ns1:RequesterID>\r\n" 
        + "<ns1:Password>" + passWord + "</ns1:Password>\r\n"
        + "</ns1:XDDSAuth>\r\n"
        + "</ns1:Authentication>\r\n";
        return authstr;
    } // end soapAuthenticate()

    
    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 3) Function soapReqForJobList : Returns the SOAP request string for job list   
    private String soapReqForJobList() {
        String jlreq = "</ns1:Request>\r\n"
        + "</tns:Header>\r\n"
        + "<tns:Body>\r\n"
        + "<ns1:JobListRequest>\r\n"
        + "<ns1:JobListRequestFilters>\r\n" 
        + "<ns1:JobEntryWindow>\r\n"
        + "<ns1:Minutes>" + faxMinutes + "</ns1:Minutes>\r\n"
        + "</ns1:JobEntryWindow>\r\n"
        + "</ns1:JobListRequestFilters>\r\n"
        + "</ns1:JobListRequest>\r\n"
        + "</tns:Body>\r\n";
        return jlreq;
    }  // end soapReqForJobList()
    
    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 4) Function soapReqForJobSummary : Returns the SOAP request string for job summary    
    private String soapReqForJobSummary() {
        String jsreq = "</ns1:Request>\r\n"
        + "</tns:Header>\r\n"
        + "<tns:Body>\r\n"
        + "<ns1:JobSummaryRequest>\r\n"
        + "<ns1:JobId>\r\n"
        + "<ns1:XDN>" + faxDomain + "</ns1:XDN>\r\n"
        + "<ns1:MRN>" + jobID + "</ns1:MRN>\r\n"
        + "</ns1:JobId>\r\n"
        + "</ns1:JobSummaryRequest>\r\n"
        + "</tns:Body>\r\n";
        return jsreq;
    }  // end soapReqForJobSummary()
    
    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 5) Function soapReqForJobDelivery : Returns the SOAP request string for job delivery    
    private String soapReqForJobDelivery() {
        String jsubreq = "</ns1:Request>\r\n"
        + "</tns:Header>\r\n"
        + "<tns:Body>\r\n"
        + "<ns1:JobDeliveryStatusRequest>\r\n"
        + "<ns1:JobId>\r\n"
        + "<ns1:XDN>" + faxDomain + "</ns1:XDN>\r\n"
        + "<ns1:MRN>" + jobID + "</ns1:MRN>\r\n"
        + "</ns1:JobId>\r\n"
        + "</ns1:JobDeliveryStatusRequest>\r\n"
        + "</tns:Body>\r\n";
        return jsubreq;
    }  // end soapReqForJobDelivery()
    

    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 6) Function soapReqForJobResend : Returns the SOAP request string for job resend   
    private String soapReqForJobResend() {
        String jresend = "</ns1:Request>\r\n"
        + "</tns:Header>\r\n"
        + "<tns:Body>\r\n"
        + "<ns1:ResendRequest xmlns=\"http://premconn.premiereconnect.com/2007/02\">\r\n"
        + "<ns1:Resend>\r\n"
        + "<ns1:JobId>\r\n"
        + "<ns1:XDN>" + faxDomain + "</ns1:XDN>\r\n"
        + "<ns1:MRN>" + jobID + "</ns1:MRN>\r\n"
        + "</ns1:JobId>\r\n"
        + "<ns1:Schedule>express</ns1:Schedule>\r\n"
        + "<ns1:ResendSpec><ns1:ItemSpec xqn=\"1\"></ns1:ItemSpec></ns1:ResendSpec>\r\n"
        + "</ns1:Resend>\r\n"
        + "</ns1:ResendRequest>\r\n"
        + "</tns:Body>\r\n";
        return jresend;
    } // end soapReqForJobResend()
 
    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   
    // 7) Function soapReqForJobCancel : Returns the SOAP request string for job resend    
    private String soapReqForJobCancel() {
        String jcancel = "</ns1:Request>\r\n"
        + "</tns:Header>\r\n"
        + "<tns:Body>\r\n"
        + "<ns1:JobCancelRequest>\r\n"
        + "<ns1:CancelItem>\r\n"
        + "<ns1:JobId>\r\n"
        + "<ns1:XDN>" + faxDomain + "</ns1:XDN>\r\n"
        + "<ns1:MRN>" + jobID + "</ns1:MRN>\r\n"
        + "</ns1:JobId>\r\n"
        + "</ns1:CancelItem>\r\n"
        + "</ns1:JobCancelRequest>\r\n"
        + "</tns:Body>";
        return jcancel;
    } // end soapReqForJobCancel()

    //-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    // 8) Function soapReqFooter : Returns the SOAP request string for job list   
    private String soapReqFooter() {
        String footer = "</tns:Envelope>\r\n";
        return footer;
    } // end soapReqFooter()
    
    // ======================================================================================================================================
    // Function soapReqAndResp : Sends SOAP request and receives the SOAP response. {Called from CONTROLLER section}  
    public String soapReqAndResp(int reqcat) {
        byte[] data = null;
        int status = 0; 
        String xmlreq = ""; 
        String xmlhdr = "";
        String xmldata = "";  
        StreamConnection xmlstream = null;
        HttpConnection connection = null;
        StringBuffer buffer = new StringBuffer();
        


    // Function soapReqAndResp : Sends SOAP request and receives the SOAP response. {Called from CONTROLLER section}  
    public String soapReqAndResp(int reqcat) {
        byte[] data = null;
        int status = 0; 
        String xmlreq = ""; 
        String xmlhdr = "";
        String xmldata = "";  
        StreamConnection xmlstream = null;
        HttpConnection connection = null;
        StringBuffer buffer = new StringBuffer();
        
        synchronized (this) {      
            try {    
                // Using BES
                xmlstream = (StreamConnection)Connector.open(SOAP_SERVER_URL + ";deviceside=false",Connector.READ_WRITE,true);
                
                connection = (HttpConnection)xmlstream;
                connection.setRequestMethod(HttpConnection.POST);
                connection.setRequestProperty("Content-Type", "text/xml; charset=utf-8");
                connection.setRequestProperty("SOAPAction",SOAP_ACTION);
                
                //  Build the Header: 
                //                          (1)                                  (2)
                xmlhdr = soapReqHeader() + soapAuthenticate();

                //  Build the Request:                     
                if (REQ_FOR_JOBLIST == reqcat) {
                    xmlreq = xmlhdr + soapReqForJobList();         // (3)
                } else if (REQ_FOR_JOBSUMMARY == reqcat) {
                    xmlreq = xmlhdr + soapReqForJobSummary();      // (4)   
                } else if (REQ_FOR_JOBDSTATUS == reqcat) {
                    xmlreq = xmlhdr + soapReqForJobDelivery();     // (5)    
                } else if (REQ_FOR_JOB_RESEND == reqcat) {
                    xmlreq = xmlhdr + soapReqForJobResend();       // (6)
                } else if (REQ_FOR_JOB_CANCEL == reqcat) {
                    xmlreq = xmlhdr + soapReqForJobCancel();       // (7)
                }
                
                xmlreq = xmlreq + soapReqFooter();   // (8)
                
                // -------------------------------- Send Request -------------------------------
               OutputStream out = connection.openOutputStream();
                out.write(xmlreq.getBytes()); // Note: 'xmlreq' is type String.
                out.flush();
                out.close();
                
                // -------------------------------- Get Response -------------------------------
                // Get the connection status
                status = connection.getResponseCode();
                if (HttpConnection.HTTP_OK != status) {
                    xmldata = "";  
                } else {
                    // Open an input stream to receive the server response
                    final InputStream instream = connection.openInputStream();
                    ...
                }
                ...

 

**Discussion:** 

1) The Java version allows you to set the connection method via:
    
 connection.setRequestMethod(HttpConnection.POST);
 connection.setRequestProperty("Content-Type", "text/xml; charset=utf-8");
 connection.setRequestProperty("SOAPAction",SOAP_ACTION);


2) Behaviors/Characteristics & Authentication are set within the SOAP envelope as shown in the sample code above.

-----

3) The following is the target & action pointers:

    
    private String SOAP_SERVER_URL = "http://myconnect.myglobal.com/soap/sync";   // <-- target
    private final String SOAP_ACTION = "http://myconnn.myconnect.com/2007/02";  // <-- action


4) I've created ObjC stubs via Apple's WSMakeStub using a * local copy * of the .WSDL file.  The code isn't pretty.


**Question #1:** Can I use the stubs and point the link to SOAP_SERVER_URL?

**Question #2:** I notice that many of the action stubs are also declared within the SOAP message (e.g., Authentication/Cancel, etc.).  

For example, Request for Job Cancel is declared in BOTH the SOAP message & the Stub:

Here's a piece of the SOAP message sent to the server:
    
// 7) Function soapReqForJobCancel : Returns the SOAP request string for job resend    
    private String soapReqForJobCancel() {
        String jcancel = "</ns1:Request>\r\n"
        + "</tns:Header>\r\n"
        + "<tns:Body>\r\n"
        + "<ns1:JobCancelRequest>\r\n"
        + "<ns1:CancelItem>\r\n"
        + "<ns1:JobId>\r\n"
        + "<ns1:XDN>" + faxDomain + "</ns1:XDN>\r\n"
        + "<ns1:MRN>" + jobID + "</ns1:MRN>\r\n"
        + "</ns1:JobId>\r\n"
        + "</ns1:CancelItem>\r\n"
        + "</ns1:JobCancelRequest>\r\n"
        + "</tns:Body>";
        return jcancel;
    } 


...apparently reflects the following stub:
    
+ (id) JobCancel:(CFTypeRef /* Complex type http://xoa.xpedite.com/2004/11/xoa|JobCancelRequest */) in_parameter in_request_header:(CFTypeRef /* Complex type http://xoa.xpedite.com/2004/11/xoa|Request */) in_request_header
{
    id result = NULL;    
    JobCancel* _invocation = [[JobCancel alloc] init];    
    [_invocation setParameters: in_parameter in_request_header:in_request_header];    
    result = _invocation resultValue] retain];    
    [_invocation release];    
    return result;    
}


**
Question 3:
**
So, do I work with the [[ObjC stubs and keep the XML Envelope as was generated in the Java version?  Or must I remove the respective XML control statements from the SOAP and go for the ObjC stubs?

**
Question 4:
**
What type of parameter to I use here?  In the XML version I have the J<nowiki/>obID and f<nowiki/>axDomain.  How would I represent it within the  s<nowiki/>etParameters statement of the J<nowiki/>obCancel stub? 

**
Question 5:
**
Is there a better/easier way to do all this (suitable for iPhone work)?

Ric.

