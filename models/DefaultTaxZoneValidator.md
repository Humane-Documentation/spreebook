<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>
  Class: Spree::DefaultTaxZoneValidator
  
    &mdash; Documentation by YARD 0.8.7.6
  
</title>

  <link rel="stylesheet" href="../css/style.css" type="text/css" charset="utf-8" />

  <link rel="stylesheet" href="../css/common.css" type="text/css" charset="utf-8" />

<script type="text/javascript" charset="utf-8">
  hasFrames = window.top.frames.main ? true : false;
  relpath = '../';
  framesUrl = "../frames.html#!Spree/DefaultTaxZoneValidator.html";
</script>


  <script type="text/javascript" charset="utf-8" src="../js/jquery.js"></script>

  <script type="text/javascript" charset="utf-8" src="../js/app.js"></script>


  </head>
  <body>
    <div id="header">
      <div id="menu">
  
    <a href="../_index.html">Index (D)</a> &raquo;
    <span class='title'><span class='object_link'><a href="../Spree.html" title="Spree (module)">Spree</a></span></span>
     &raquo; 
    <span class="title">DefaultTaxZoneValidator</span>
  

  <div class="noframes"><span class="title">(</span><a href="." target="_top">no frames</a><span class="title">)</span></div>
</div>

      <div id="search">
  
    <a class="full_list_link" id="class_list_link"
        href="../class_list.html">
      Class List
    </a>
  
    <a class="full_list_link" id="method_list_link"
        href="../method_list.html">
      Method List
    </a>
  
    <a class="full_list_link" id="file_list_link"
        href="../file_list.html">
      File List
    </a>
  
</div>
      <div class="clear"></div>
    </div>

    <iframe id="search_frame"></iframe>

    <div id="content"><h1>Class: Spree::DefaultTaxZoneValidator
  
  
  
</h1>

<dl class="box">
  
    <dt class="r1">Inherits:</dt>
    <dd class="r1">
      <span class="inheritName">ActiveModel::Validator</span>
      
        <ul class="fullTree">
          <li>Object</li>
          
            <li class="next">ActiveModel::Validator</li>
          
            <li class="next">Spree::DefaultTaxZoneValidator</li>
          
        </ul>
        <a href="#" class="inheritanceTree">show all</a>
      
      </dd>
    
  
  
    
  
    
  
  
  
    <dt class="r2 last">Defined in:</dt>
    <dd class="r2 last">tax_rate.rb</dd>
  
</dl>
<div class="clear"></div>








  
    <h2>
      Instance Method Summary
      <small>(<a href="#" class="summary_toggle">collapse</a>)</small>
    </h2>

    <ul class="summary">
      
        <li class="public ">
  <span class="summary_signature">
    
      <a href="#validate-instance_method" title="#validate (instance method)">- (Object) <strong>validate</strong>(record) </a>
    

    
  </span>
  
  
  
  
  
  
  

  
    <span class="summary_desc"><div class='inline'></div></span>
  
</li>

      
    </ul>
  


  

  <div id="instance_method_details" class="method_details_list">
    <h2>Instance Method Details</h2>

    
      <div class="method_details first">
  <h3 class="signature first" id="validate-instance_method">
  
    - (<tt>Object</tt>) <strong>validate</strong>(record) 
  

  

  
</h3><table class="source_code">
  <tr>
    <td>
      <pre class="lines">


3
4
5
6
7</pre>
    </td>
    <td>
      <pre class="code"><span class="info file"># File 'tax_rate.rb', line 3</span>

<span class='kw'>def</span> <span class='id identifier rubyid_validate'>validate</span><span class='lparen'>(</span><span class='id identifier rubyid_record'>record</span><span class='rparen'>)</span>
  <span class='kw'>if</span> <span class='id identifier rubyid_record'>record</span><span class='period'>.</span><span class='id identifier rubyid_included_in_price'>included_in_price</span>
    <span class='id identifier rubyid_record'>record</span><span class='period'>.</span><span class='id identifier rubyid_errors'>errors</span><span class='period'>.</span><span class='id identifier rubyid_add'>add</span><span class='lparen'>(</span><span class='symbol'>:included_in_price</span><span class='comma'>,</span> <span class='const'>Spree</span><span class='period'>.</span><span class='id identifier rubyid_t'>t</span><span class='lparen'>(</span><span class='symbol'>:included_price_validation</span><span class='rparen'>)</span><span class='rparen'>)</span> <span class='kw'>unless</span> <span class='const'>Zone</span><span class='period'>.</span><span class='id identifier rubyid_default_tax'>default_tax</span>
  <span class='kw'>end</span>
<span class='kw'>end</span></pre>
    </td>
  </tr>
</table>
</div>
    
  </div>

</div>

    <div id="footer">
  Generated on Fri May 15 11:43:03 2015 by
  <a href="http://yardoc.org" title="Yay! A Ruby Documentation Tool" target="_parent">yard</a>
  0.8.7.6 (ruby-2.0.0).
</div>

  </body>
</html>