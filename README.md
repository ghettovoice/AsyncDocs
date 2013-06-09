AsyncDocs
=========

MODx Evo Plugin to ajaxify site

============================================

Author: Vladimir Vershinin<br />
License: http://www.gnu.org/licenses/gpl-2.0.html GNU General Public License<br />

Example site: http://new.mcinterior.ru/

INSTALLATION
--------------------------------------------------------------------------
1. Extract plugin archive
2. Copy "asyncdocs" folder from extracted archive to [modxDir]/assets/plugins/ directory
3. Create a new plugin in the manager called "AsyncDocs" and copy/paste the contents of plugin.txt
into the code field.
4. Check "OnWebPageInit" and "OnCacheUpdate" events at the System Events tab.
5. Copy/paste to the config field on the Config tab:<br />
    <pre>
    &Configuration:=ajaxPageLoader;; &contentSelector=XPath to the content DOM element;string; &fields=Document fields(list of doc fields to add to the response separated by "||");textarea;pagetitle||longtitle||description &excludeChunks=Exclude chunks(list of chunks to exclude from document content separated by "||");textarea
    </pre>
6. Set needed plugin options:<br />
    &contentSelector - XPath to the content DOM element<br />
    &fields - Document fields(list of doc fields to add to the response separated by "||")<br />
    &excludeChunks - Exclude chunks(list of chunks to exclude from document content separated by "||")


USAGE
--------------------------------------------------------------------------
As a general rule for asynchronous navigating pages need only change the page's content.
Plugin allows to do this in 2 ways:
1. Move the immutable part of a template in chunks (e. g: header and footer) and list these chunks in &excludeChunks option.
2. Set the XPath to the content DOM element. This element will be extracted from document output.

To get page via ajax make an ajax request to url of the page that you need. One field
in request data is necessary: "AsyncDocs".
The plugin returns a json object: <br />
<pre>
{
    content: string,                // document output 
    dir: string,                    // document tree direction, "up"(up the documents tree) or "down"(down the document tree)
    fields: {},                     // additional document fields
    fromCache: boolean,             // cache or generated output 
    id: int,                        // document id 
    idx: int,                       // document menuindex 
    lvl: int,                       // depth level of the document 
    prevId: int,                    // previous document id
    prevIdx: int,                   // previous document menuindex 
    prevLvl: int,                   // previous document depth level
    status: int,                    // HTTP status of response; 200 - OK, 404 - not found, 301 - moved for references
    treePath: []                    // array of ids from root as '0' to current document id
}
</pre>

Plugins that invoked on "OnPageUnauthorized" and "OnPageNotFound" events
must return id of document to load forward. In this events use $asyncDocs->isAjax() to
determine that this is ajax page request.

Example:
<pre>
// some code here that determine id of the landing document and setting $_REQUEST vars

// landingDocId is defined and this is ajax request than return
// to AsyncDocs plugin id of landing document to load
if ($landingDocId && $asyncDocs->isAjax()) { 
    return $landingDocId; 
}
// landingDocId is defined, not ajax
elseif ($landingDocId) {
    $modx->sendForward($landingDocId);
}

</pre>

