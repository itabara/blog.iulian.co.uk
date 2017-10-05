---
title: Asp.Net Page Life Cycle
author: Iulian
type: post
date: 2008-02-01T10:28:00+00:00
url: /2008/02/asp-net-page-life-cycle/
categories:
  - Asp.net
  - Web Forms
tags:
  - Life cycle
  - Page
  - Web Forms

---
<p align="justify">
  When an ASP.NET page runs, the page goes through a life cycle in which it performs a series of processing steps. These include initialization, instantiating controls, restoring and maintaining state, running event handler code and rendering. The Life cycle of an ASP.NET page depends on whether the page is requested for the <strong>first time</strong> or it is a<strong><em> </em>postback</strong>. <strong>Postback</strong> is a process by which a page can request for itself.
</p>

#### General Page Life-cycle Stages

In general terms, the page goes through the stages outlined in the following table. In addition to the page life-cycle stages, there are application stages that occur before and after a request but are not specific to a page.

<table cellspacing="0" cellpadding="2" border="1">
  <tr>
    <td valign="top" width="176">
      <h5>
        <strong>Stage</strong>
      </h5>
    </td>
    
    <td valign="top">
      <h5>
        <strong>Description</strong>
      </h5>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Page request</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        The page request occurs before the page life cycle begins. When the page is requested by a user, ASP.NET determines whether the page needs to be parsed and compiled or whether a cached version of the page can be sent in response without running the page.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Start</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        In the start step, page properties such as <strong>Request</strong> and <strong>Response</strong> are set. At this stage, the page also determines whether the request is a <strong>Postback</strong> or a new request and sets the <strong>IsPostBack </strong>property. Additionally, during the start step, the page&#8217;s <strong>UICulture</strong> property is set.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Page initialization</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        During page initialization, controls on the page are available and each control&#8217;s <strong>UniqueID</strong> property is set. Any themes are also applied to the page. If the current request is a <strong>Postback</strong>, the postback data has not yet been loaded and control property values have not been restored to the values from view state.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Load</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        During load, if the current request is a <strong>Postback</strong>, control properties are loaded with information recovered from view state and control state.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Validation</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        During validation, the <strong>Validate</strong> method of all validator controls is called, which sets the <strong>IsValid</strong> property of individual validator controls and of the page.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Postback event handling</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        If the request is a <strong>postback</strong>, any event handlers are called.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Rendering</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        Before rendering, view state is saved for the page and all controls. During the rendering phase, the page calls the <strong>Render </strong>method for each control, providing a text writer that writes its output to the <strong>OutputStream</strong> of the page&#8217;s <strong>Response</strong> property.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="176">
      <strong>Unload</strong>
    </td>
    
    <td valign="top">
      <p align="justify">
        <strong>Unload</strong> is called after the page has been fully rendered, sent to the client, and is ready to be discarded. At this point, page properties such as <strong>Response</strong> and <strong>Request</strong> are unloaded and any cleanup is performed.
      </p>
    </td>
  </tr>
</table>

#### Life-cycle Events

<p align="justify">
  Within each stage of the life cycle of a page, the page raises events that you can handle to run your own code. For control events, you bind the event handler to the event, either declarative using attributes such as <strong>onClick</strong>, or in <strong>code</strong>.
</p>

<p align="justify">
  Pages also support automatic event wire-up, meaning that ASP.NET looks for methods with particular names and automatically runs those methods when certain events are raised. If the <strong>AutoEventWireup</strong> attribute of the <strong>@Page</strong> directive is set to <strong>true</strong> (or if it is not defined, since by default it is <strong>true</strong>), page events are automatically bound to methods that use the naming convention of <strong>Page_event</strong>, such as <strong>Page_Load</strong> and <strong>Page_Init</strong>.
</p>

<p align="justify">
  The following table lists the page life-cycle events that you will use most frequently. There are more events than those listed; however, they are not used for most page processing scenarios. Instead, they are primarily used by server controls on the ASP.NET Web page to initialize and render themselves.
</p>

#### Common Life-cycle Events

<table cellspacing="0" cellpadding="2" border="1">
  <tr>
    <td width="196" align="center">
      <h5 style="text-align: left" align="center">
        Page Event
      </h5>
    </td>
    
    <td width="481" align="center">
      <h5 style="text-align: left" align="center">
        Typical Use
      </h5>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="197">
      <strong>PreInit</strong>
    </td>
    
    <td valign="top" width="484">
      Use this event for the following:&nbsp; </p> 
      
      <ol>
        <li>
          Check the IsPostBack property to determine whether this is the first time the page is being processed. <li>
            Create or re-create dynamic controls. <li>
              Set a master page dynamically. <li>
                Set the Theme property dynamically. <li>
                  Read or set profile property values.
                </li></ol> 
                <p align="justify">
                  <em>Note: If the request is a <strong>postback</strong>, the values of the controls have not yet been restored from view state. If you set a control property at this stage, its value might be overwritten in the next event.</em>
                </p></td> </tr> 
                
                <tr>
                  <td valign="top" width="198">
                    <strong>Init</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    <p align="justify">
                      Raised after all controls have been initialized and any skin settings have been applied. Use this event to read or initialize control properties.
                    </p>
                  </td>
                </tr>
                
                <tr>
                  <td valign="top" width="199">
                    <strong>InitComplete</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    <p align="justify">
                      Raised by the <strong>Page</strong> object. Use this event for processing tasks that require all initialization be complete.
                    </p>
                  </td>
                </tr>
                
                <tr>
                  <td valign="top" width="199">
                    <strong>PreLoad</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    <p align="justify">
                      Use this event if you need to perform processing on your page or control before the Load event. After the <strong>Page</strong> raises this event, it loads view state for itself and all controls, and then processes any <strong>postback</strong> data included with the Request instance.
                    </p>
                  </td>
                </tr>
                
                <tr>
                  <td valign="top" width="199">
                    <strong>Load</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    <p align="justify">
                      The Page calls the <strong>OnLoad</strong> event method on the <strong>Page</strong>, then recursively does the same for each child control, which does the same for each of its child controls until the page and all controls are loaded.
                    </p>
                  </td>
                </tr>
                
                <tr>
                  <td valign="top" width="199">
                    <strong>Control events</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    <p align="justify">
                      Use these events to handle specific control events, such as a Button control&#8217;s Click event or a <strong>TextBox</strong> control&#8217;s <strong>TextChanged</strong> event. In a postback request, if the page contains validator controls, check the <strong>IsValid</strong> property of the <strong>Page</strong> and of individual validation controls before performing any processing.
                    </p>
                  </td>
                </tr>
                
                <tr>
                  <td valign="top" width="199">
                    <strong>LoadComplete</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    <p align="justify">
                      Use this event for tasks that require that all other controls on the page be loaded.
                    </p>
                  </td>
                </tr>
                
                <tr>
                  <td valign="top" width="199">
                    <strong>PreRender</strong>
                  </td>
                  
                  <td valign="top" width="479">
                    Before this event occurs:&nbsp; </p> 
                    
                    <ol>
                      <li>
                        <div align="justify">
                          The Page object calls <strong>EnsureChildControls</strong> for each control and for the page.
                        </div>
                        
                        <li>
                          <div align="justify">
                            Each data bound control whose <strong>DataSourceID</strong> property is set calls its <strong>DataBind</strong> method.
                          </div>
                          
                          <li>
                            <div align="justify">
                              The <strong>PreRender</strong> event occurs for each control on the page. Use the event to make final changes to the contents of the page or its controls.
                            </div>
                          </li></ol> </td> </tr> 
                          
                          <tr>
                            <td valign="top" width="199">
                              <strong>SaveStateComplete</strong>
                            </td>
                            
                            <td valign="top" width="479">
                              <p align="justify">
                                Before this event occurs, <strong>ViewState</strong> has been saved for the page and for all controls. Any changes to the page or controls at this point will be ignored. Use this event perform tasks that require view state to be saved, but that do not make any changes to controls.
                              </p>
                            </td>
                          </tr>
                          
                          <tr>
                            <td valign="top" width="199">
                              <strong>Render</strong>
                            </td>
                            
                            <td valign="top" width="479">
                              <p align="justify">
                                This is not an event; instead, at this stage of processing, the <strong>Page</strong> object calls this method on each control. All ASP.NET Web server controls have a Render method that writes out the control&#8217;s markup that is sent to the browser. If you create a custom control, you typically override this method to output the control&#8217;s markup. However, if your custom control incorporates only standard ASP.NET Web server controls and no custom markup, you do not need to override the <strong>Render</strong> method. A user control (an .ascx file) automatically incorporates rendering, so you do not need to explicitly render the control in code.
                              </p>
                            </td>
                          </tr>
                          
                          <tr>
                            <td valign="top" width="199">
                              <strong>Unload</strong>
                            </td>
                            
                            <td valign="top" width="483">
                              <p align="justify">
                                This event occurs for each control and then for the page. In controls, use this event to do final cleanup for specific controls, such as closing control-specific database connections. For the page itself, use this event to do final cleanup work, such as closing open files and database connections, or finishing up logging or other request-specific tasks.
                              </p>
                              
                              <p align="justify">
                                <em>Note: During the unload stage, the page and its controls have been rendered, so you cannot make further changes to the response stream. If you attempt to call a method such as the Response.Write method, the page will throw an exception.</em>
                              </p>
                            </td>
                          </tr></tbody> </table> 
                          
                          <p>
                            The events associated with the relevant page cycle phases are:
                          </p>
                          
                          <ol>
                            <li>
                              Page Initialization: <strong>Page_Init</strong> <li>
                                View State Loading: <strong>LoadViewState</strong> <li>
                                  Postback data processing: <strong>LoadPostData</strong> <li>
                                    Page Loading: <strong>Page_Load</strong> <li>
                                      PostBack Change Notification: <strong>RaisePostDataChangedEvent</strong> <li>
                                        PostBack Event Handling: <strong>RaisePostBackEvent</strong> <li>
                                          Page Pre Rendering Phase: <strong>Page_PreRender</strong> <li>
                                            View State Saving: <strong>SaveViewState</strong> <li>
                                              Page Rendering: <strong>Page_Render</strong> <li>
                                                Page Unloading: <strong>Page_UnLoad</strong>
                                              </li></ol>