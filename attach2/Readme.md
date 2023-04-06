# Attachments 2 for Cotonti

With this plugin you can attach files and images to any Cotonti objects including pages, forum posts and organize them into galleries or downloads.
<p>With this plugin you can attach files and images to any Cotonti objects including pages, forum posts and organize them into galleries or downloads.</p>

<h2>Features</h2>

<ul>
	<li>Modern upload dialog based on <a href="http://blueimp.github.com/jQuery-File-Upload/">jQuery File Upload</a>.</li>
	<li>Multi-upload support.</li>
	<li>Easy integration via TPL callbacks.</li>
	<li>Built-in gallery based on <a href="http://lokeshdhakar.com/projects/lightbox2/">Lightbox 2</a>.</li>
	<li>File downloads are counted and provided with original file names.</li>
	<li>Configurable on-the-fly thumbnail generation.</li>
	<li>Special BBcodes to paste images into content regardless of parser.</li>
</ul>

<h2>Installation</h2>

<ol>
	<li>Download the plugin and extract &quot;attach2&quot; folder into your Cotonti plugins folder.</li>
	<li>Go to Administration / Extensions and install the Attachments plugin.</li>
	<li>Enter plugin Configuration and set the proper defaults there.</li>
	<li>Create the &quot;Directory for files&quot; on your host (by default it is &quot;datas/attach&quot;) and make it writable for PHP (e.g. CHMOD 775 or CHMOD 777).</li>
</ol>

<h2>Basic usage</h2>

<p>This section covers plugin usage basics with Pages module and minimal customizations involved. Similar actions can be applied to other content such as Forums, Comments, etc.</p>

<p>All examples are given for the basic theme, feel free to customize them for your needs.</p>

<h3>Adding the attachment dialog</h3>

<p>First we need to be able to attach files to pages. You would expect to do it while editing the page itself, but in this plugin it can be done while browsing the page. So let&#39;s add an &quot;Attach files&quot; button to the page management menu in &quot;<em>page.tpl&quot;</em>:</p>

<p><img alt="Link to attachment management" src="http://www.cotonti.com/datas/users/att2-link_5.jpg" style="width:541px;height:187px;" /></p>

<p>It can be done by adding following code in your <em>&quot;page.tpl&quot;</em>:</p>

<pre class="brush:xml;">
&lt;!-- IF {PHP|cot_auth(&#39;plug&#39;, &#39;attach2&#39;, &#39;W&#39;)} --&gt;
&lt;li&gt;{PAGE_ID|att_widget(&#39;page&#39;,$this,&#39;attach2.link&#39;)}&lt;/li&gt;
&lt;!-- ENDIF --&gt;</pre>

<p>The link will only be displayed to users who have permission to attach files (controlled by Write access to Attachments plugin).</p>

<h3>Using the attachment dialog</h3>

<p>If you click the &quot;Attach files&quot; link, it opens attachment dialog:</p>

<p><a href="http://www.cotonti.com/datas/users/att2-widget_5.jpg"><img alt="Attachment dialog" src="http://www.cotonti.com/datas/users/att2-widget-thumb_5.jpg" style="width:580px;height:386px;" /></a></p>

<p>The dialog is pretty straightforward. It lets you add one or several files at once, upload them, remove existing attachments, replace files and edit descriptions via AJAX. Drag &amp; Drop rows to reorder them. Once you&#39;re done with changes, click the &quot;Close&quot; button and it will refresh the page to show you results.</p>

<h3>Displaying all files at once</h3>

<p>The quickest way to display all files attached to a page is by using att_display() widget in <em>&quot;page.tpl&quot;</em>:</p>

<pre class="brush:xml;">
&lt;!-- IF {PAGE_ID|att_count(&#39;page&#39;,$this)} &gt; 0 --&gt;
&lt;div class=&quot;block&quot;&gt;
	&lt;h3&gt;{PHP.L.att_attachments}&lt;/h3&gt;
	{PAGE_ID|att_display(&#39;page&#39;,$this)}
&lt;/div&gt;
&lt;!-- ENDIF --&gt;</pre>

<p>It renders to something like this:</p>

<p><img alt="Attachments display all example" src="http://www.cotonti.com/datas/users/att2-display_5.jpg" style="width:659px;height:273px;" /></p>

<h3>Adding a simple gallery</h3>

<p>One of the most common tasks you&#39;d like to complete is turning a page into an image gallery. You can add a simple gallery to <em>&quot;page.tpl&quot;</em> by pasting this code:</p>

<pre class="brush:xml;">
&lt;!-- IF {PAGE_ID|att_count(&#39;page&#39;,$this,&#39;images&#39;)} &gt; 0 --&gt;
&lt;div class=&quot;block&quot;&gt;
	&lt;h3&gt;{PHP.L.att_gallery}&lt;/h3&gt;
	{PAGE_ID|att_gallery(&#39;page&#39;,$this)}
&lt;/div&gt;
&lt;!-- ENDIF --&gt;</pre>

<p>It embeds a <a href="http://lokeshdhakar.com/projects/lightbox2/">Lightbox 2</a> gallery into a page:</p>

<p><img alt="Attachments 2 gallery example" src="http://www.cotonti.com/datas/users/att2-gallery_5.jpg" style="width:571px;height:493px;" /></p>

<h3>Adding downloadable files block</h3>

<p>Another common task is providing multiple files for downloading on a page. Use att_downloads() widget to embed it in <em>&quot;page.tpl&quot;</em>:</p>

<pre class="brush:xml;">
&lt;!-- IF {PAGE_ID|att_count(&#39;page&#39;,$this,&#39;files&#39;)} &gt; 0 --&gt;
&lt;div class=&quot;block&quot;&gt;
	&lt;h3&gt;{PHP.L.att_downloads}&lt;/h3&gt;
	{PAGE_ID|att_downloads(&#39;page&#39;,$this)}
&lt;/div&gt;
&lt;!-- ENDIF --&gt;</pre>

<p>A typical downloads block renders like this:</p>

<p><img alt="Attachments 2 downloads block example" src="http://www.cotonti.com/datas/users/att2-downloads_5.jpg" style="width:409px;height:170px;" /></p>

<h2>Advanced usage</h2>

<p>Pasting basic widgets into pages is easy. But what if you need something custom or if you are curious about how they work? This section has the answers.</p>

<h3>TPL callbacks and parameters</h3>

<p>Attachments are not limited to just pages and the standard templates. You can easily attach files to other content and customize templates. Let&#39;s have a look at TPL callbacks this plugin provides and parameters they have.</p>

<h4>att_widget()</h4>

<pre class="brush:php;">
/**
 * Generates a file upload/edit widget.
 * Use it as CoTemplate callback.
 * @param  string  $area    Target module/plugin code.
 * @param  integer $item    Target item id.
 * @param  string  $tpl     Template code
 * @return string           Rendered widget
 */
function att_widget($area, $item, $tpl = &#39;attach2.widget&#39;, $width = &#39;100%&#39;, $height = &#39;200&#39;)</pre>

<p>This callback is used to add the attachments management dialog to the content object. In previous chapter we used it to add a &quot;Attach files&quot; link together with the dialog to pages by editing <em>page.tpl</em>. In fact this callback is more powerful, look at its parameters:</p>

<ul>
	<li>$area - Defines the type of content the files are attached to. Usually conains module or plugin code, such as &#39;page&#39;, &#39;forums&#39;, &#39;comments&#39;, etc.</li>
	<li>$item - This parameter accepts an integer ID of the item the files are attached to. For example, it can be a page ID, forums post ID or comment ID. <a href="http://www.cotonti.com/docs/ext/themes/cotemplate_statements#ch2.2">CoTemplate pipe operator</a> and &quot;<em>$this</em>&quot; keyword are used to pass the appropriate tag to this parameter.</li>
	<li>$tpl - Contains template code for the widget to be rendered. E.g. if you pass it <em>&#39;attach2.my_widget&#39;</em>, it will try to find the template in <em>&quot;themes/your_theme/plugins/attach2.my_widget.tpl&quot;</em>. Default (fallback) templates can be found in <em>&quot;plugins/attach2/tpl&quot;</em>, use them as reference. <em>&#39;attach2.widget&#39;</em> inserts the dialog as an iframe, whereas <em>&#39;attach2.link&#39;</em> (see previous chapter) adds a link + popup dialog.</li>
	<li>$width - Width of the iframe, optional.</li>
	<li>$height - Height of the iframe, optional.&nbsp;</li>
</ul>

<p>For example, here is how you can add &quot;Attach files&quot; link for forum posts in <em>&quot;forums.posts.tpl&quot;</em> inside the <em>FORUMS_POSTS_ROW</em> block:</p>

<pre class="brush:xml;">
&lt;!-- IF {FORUMS_POSTS_ROW_USERID} == {PHP.usr.id} --&gt;
{FORUMS_POSTS_ROW_ID|att_widget(&#39;forums&#39;,$this,&#39;attach2.link&#39;)}
&lt;!-- ENDIF --&gt;</pre>

<h4>att_display()</h4>

<pre class="brush:php;">
/**
 * Renders attached items on page
 * @param  string  $area Target module/plugin code
 * @param  integer $item Target item id
 * @param  string  $tpl  Template code
 * @param  string  $type Attachment type filter: &#39;files&#39;, &#39;images&#39;. By default includes all attachments.
 * @return string        Rendered output
 */
function att_display($area, $item, $tpl = &#39;attach2.display&#39;, $type = &#39;all&#39;)</pre>

<p>This callback is used to display files already attached to an item. It can render them all within one stream or include a certain type of files only. The paramters are similar with other callbacks:</p>

<ul>
	<li>$area - Site area, same as for att_widget().</li>
	<li>$item - Item ID, same as for att_widget().</li>
	<li>$tpl - Template code, similar to att_widget(). The standard one for this widget is called &#39;attach2.display&#39; and can be found in <em>&quot;plugins/attach2/tpl/attach2.display.tpl&quot;</em>.</li>
	<li>$type - Use this parameter to display images only or files only.</li>
</ul>

<p>Here is how you can display all files attached to a forums post in <em>&quot;forums.posts.tpl&quot;</em> inside the <em>FORUMS_POSTS_ROW</em> block:</p>

<pre class="brush:xml;">
{FORUMS_POSTS_ROW_ID|att_display(&#39;forums&#39;,$this)}</pre>

<h4>att_count()</h4>

<pre class="brush:php;">
/**
 * Returns number of attachments for a specific item.
 * @param  string  $area Target module/plugin code
 * @param  integer $item Target item id
 * @param  string  $type Attachment type filter: &#39;files&#39;, &#39;images&#39;. By default includes all attachments.
 * @return integer       Number of attachments
 */
function att_count($area, $item, $type = &#39;all&#39;)</pre>

<p>This callback returns the number of files attached to an item but it is often used to check whether there are files attached or not. Parameters are common:</p>

<ul>
	<li>$area - Site area, same as for att_widget().</li>
	<li>$item - Item ID, same as for att_widget().</li>
	<li>$type - Use this parameter to count images only or files only.</li>
</ul>

<p>Here is how you can use it to display attachments in &quot;forums.posts.tpl&quot; only if there are files attached:</p>

<pre class="brush:xml;">
&lt;!-- IF {FORUMS_POSTS_ROW_ID|att_count(&#39;forums&#39;,$this)} &gt; 0 --&gt;
{FORUMS_POSTS_ROW_ID|att_display(&#39;forums&#39;,$this)}
&lt;!-- ENDIF --&gt;</pre>

<h4>att_gallery()</h4>

<pre class="brush:php;">
/**
 * Renders images only as a gallery.
 * @param  string  $area Target module/plugin code
 * @param  integer $item Target item id
 * @param  string  $tpl  Template code
 * @return string        Rendered output
 */
function att_gallery($area, $item, $tpl = &#39;attach2.gallery&#39;)</pre>

<p>This is a shortcut to att_display() using &#39;attach2.gallery&#39; as $tpl by default and $type set to &#39;images&#39;. By default it shows you how to make a simple gallery using Lightbox 2, but you can make your own template and use any gallery script you wish. Parameters:</p>

<ul>
	<li>$area - Site area, same as for att_widget().</li>
	<li>$item - Item ID, same as for att_widget().</li>
	<li>$tpl - Template code, see att_widget() for description.</li>
</ul>

<p>E.g. let&#39;s imagine we&#39;ve made our own template located in &quot;themes/your_theme/plugins/attach2.fancy-gallery.tpl&quot;. Here&#39;s how we attach it to <em>LIST_ROW</em> block in <em>&quot;page.list.tpl&quot;</em>:</p>

<pre class="brush:xml;">
{LIST_ROW_ID|att_gallery(&#39;page&#39;,$this,&#39;attach2.fancy-gallery&#39;)}</pre>

<h4>att_downloads()</h4>

<pre class="brush:php;">
/**
 * Renders files only as downloads block.
 * @param  string  $area Target module/plugin code
 * @param  integer $item Target item id
 * @param  string  $tpl  Template code
 * @return string        Rendered output
 */
function att_downloads($area, $item, $tpl = &#39;attach2.downloads&#39;)</pre>

<p>Similarly to att_gallery(), this is a shortcut to att_display() using &#39;attach2.downloads&#39; as $tpl by default and $type set to &#39;files&#39;. It is normally used to provide downloadable files section. Parameters:</p>

<ul>
	<li>$area - Site area, same as for att_widget().</li>
	<li>$item - Item ID, same as for att_widget().</li>
	<li>$tpl - Template code, see att_widget() for description.</li>
</ul>

<p>E.g. let&#39;s imagine we&#39;ve made our own template located in &quot;themes/your_theme/plugins/attach2.download-box.tpl&quot;. Here&#39;s how we attach it to a page in <em>&quot;page.tpl&quot;</em>:</p>

<pre class="brush:xml;">
{PAGE_ID|att_downloads(&#39;page&#39;,$this,&#39;attach2.download-box&#39;)}</pre>

<h4>att_thumb()</h4>

<div>/**</div>

<div>&nbsp;* Returns attachment thumbnail path. Generates the thumbnail first if</div>

<div>&nbsp;* it does not exist.</div>

<div>&nbsp;* @param &nbsp;mixed &nbsp; $id &nbsp; &nbsp; Attachment ID or a row returned by att_get() function.</div>

<div>&nbsp;* @param &nbsp;integer $width &nbsp;Thumbnail width in pixels</div>

<div>&nbsp;* @param &nbsp;integer $height Thumbnail height in pixels</div>

<div>&nbsp;* @param &nbsp;string &nbsp;$frame &nbsp;Framing mode: &#39;width&#39;, &#39;height&#39;, &#39;auto&#39; or &#39;crop&#39;</div>

<div>&nbsp;* @return string &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Thumbnail path on success or false on error</div>

<div>&nbsp;*/</div>

<p>function att_thumb($id, $width = 0, $height = 0, $frame = &#39;&#39;)</p>

<p>This callback is mostly used in attach2 templates themselves. It generates a thumbnail for an image attachment and returns an URL to that thumbnail. The parameters are:</p>

<ul>
	<li>$id - Attachment ID.</li>
	<li>$width - Max. thumbnail width in pixels.</li>
	<li>$height - Max. thumbnail height in pixels.</li>
	<li>$frame - Framing mode. &#39;width&#39; fits to the width and saves image ratio, &#39;height&#39; fits to the height and saves image ratio. &#39;auto&#39; (default) keeps the original ratio and fits the image into the given rectangle, &#39;crop&#39; crops the image into the exact width/height given.</li>
</ul>

<p>Thumbnails are generated on the fly and cached for futher use. Here is an example call from attach2.gallery.tpl:</p>

<pre class="brush:xml;">
&lt;img src=&quot;{ATTACH_ROW_ID|att_thumb($this,200,200,&#39;crop&#39;)}&quot; alt=&quot;{ATTACH_ROW_FILENAME}&quot; /&gt;</pre>

<h4>att_get()</h4>

<pre class="brush:php;">
/**
 * Fetches a single attachment object for a given item.
 * @param  string  $area   Target module/plugin code.
 * @param  integer $item   Target item id.
 * @param  string  $column Empty string to return full row, one of the following to return a single value: &#39;id&#39;, &#39;user&#39;, &#39;path&#39;, &#39;filename&#39;, &#39;ext&#39;, &#39;img&#39;, &#39;size&#39;, &#39;title&#39;, &#39;count&#39;
 * @param  string  $number Attachment number within item, or one of these values: &#39;first&#39;, &#39;rand&#39; or &#39;last&#39;. Defines which image is selected.
 * @return mixed           Scalar column value, entire row as array or NULL if no attachments found.
 */
function att_get($area, $item, $column = &#39;&#39;, $mode = &#39;first&#39;)</pre>

<p>This function is used to get data for a single attachment per item. Its main purpose is to create page covers in category lists. Parameters are:</p>

<ul>
	<li>$area - Site area, same as for att_widget().</li>
	<li>$item - Item ID, same as for att_widget().</li>
	<li>$column - Name of the column to be returned by the function. If empty, the function returns an array with a row from the database. If one of the specified values is passed, the scalar column value is returned.</li>
	<li>$number - Affects the ordering. By default the first attachment is returned. You can change it to last with &#39;last&#39; or to a random one with &#39;random&#39;. You can also specify exact number of the attachment for the item, e.g. $number == 3 fetches the third image.</li>
</ul>

<p>Here is an example of how this function can be used to turn a first attachment per page into clickable page cover thumbnails in <em>&quot;page.list.tpl&quot;</em>:</p>

<pre class="brush:xml;">
&lt;a href=&quot;{LIST_ROW_ID|att_get(&#39;page&#39;,$this,&#39;path&#39;)}&quot; title=&quot;{LIST_ROW_ID|att_get(&#39;page&#39;,$this,&#39;title&#39;)}&quot;&gt;
    &lt;img src=&quot;{LIST_ROW_ID|att_get(&#39;page&#39;,$this)|att_thumb($this,240,160)}&quot; alt=&quot;Foobar&quot; /&gt;
&lt;/a&gt;</pre>

<p>As you see, this function can be used in conjunction with att_thumb() to generate thumbnails on the fly. The minimal Cotonti version for this feature to work is 0.9.12.</p>

<h3>BBcodes for embedding into content</h3>

<p>Images and thumbnails of the attachments can be pasted directly into content such as page_text, com_text, etc. However, you need to get the Attachment ID somehow first in order to paste it. The syntax of bbcode parameters is similar to URL query string. Available bbcodes are:</p>

<ul>
	<li>[att_thumb] - inserts only a thumbnail &lt;img/&gt; tag.</li>
	<li>[att_image] - inserts a clickable image conisting of an &lt;img/&gt; thumbnail and a hyperlink to a full image.</li>
</ul>

<p>Usage example:</p>

<pre class="brush:plain;">
[att_thumb?id=15]
[att_image?id=11&amp;width=320&amp;height=240&amp;alt=Picture description&amp;class=foo]</pre>

<p>These bbcodes work regardless of the parser being used, including HTML and Markdown. If you want to disable them, just Pause the attach2.parser part of this plugin in Administration / Extensions / Attachments.</p>

<h3>Cleanup tool</h3>

<p>There&#39;s a tool in Administration / Other / Attachments. It is used to purge files attached to items which don&#39;t exist anymore. Sometimes items are removed but the code removing linked attachmehts is missing, so this tool may help.</p>

<h2>Contact</h2>

<p>Please use the <a href="http://www.cotonti.com/forums?m=topics&amp;s=3">Extensions forum</a> to discuss plugin features and <a href="https://github.com/trustmaster/cot-attach2/issues">GitHub tracker</a> to submit bug reports.</p>




