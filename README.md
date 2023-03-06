# Quick Navigation on Google with ShortKeys

1. Install Google Chrome extension [Shortkeys](https://chrome.google.com/webstore/detail/shortkeys-custom-keyboard/logpjaacgmcbpdkdchjiaagddngobkck)
2. Click on the ShortKeys menu and select "Options"
3. Add a shortcut using the "Add shortcut" button.

> A blank shortcut is available for use whenever you just installed the application

4. Enter these settings below for the shortcut:

- *Shortcut*: `tab`
- *Label*: `Result Picker` or any other string of your choice
- *Behavior*: `Run JavaScript`

5. Expand the Shortcut you added by clicking the chevron `>` on the left side of the shortcut row

> This would let you edit the *Shortcut Settings* and the *Activation Settings*

- *Active while in inputs*: [checked]
- *Dropdown*: `Only on specific sites`
- *Textarea*: `*.google.com*`

> JavaScript Code can also be seen in `main.js` in this repository

*JavaScript Code*:

```javascript
const querySelector = 'div.yuRUbf a h3, div.usJj9c h3 a';
const searchResultsArray = Array.from(document.querySelectorAll(querySelector)).filter(node =>
    !node.closest('.Wt5Tfe')
);
document.selectedIndexInSearchResultsArray = 0;

function selectResult(newIndex) {
    document.selectedIndexInSearchResultsArray = newIndex;
    let link = findLinkByIndex(newIndex);
    link.focus();
}

function findLinkByIndex(newIndex) {
    if (newIndex < 0 || newIndex >= searchResultsArray.length) {
        return  // future idea: modify for next/prev page
    }

    let searchResult = searchResultsArray[newIndex];

    if (searchResult.parentNode.nodeName.toLowerCase() === 'a') {
        return searchResult.parentNode;
    } else if (searchResult.nodeName.toLowerCase() === 'a') {
        return searchResult;
    } else {
        throw 'Expected an <a> with an embedded <h3> or an <h3> with an embedded <a> but instead got ' + searchResult;
    }
}

document.onkeydown = function(event) {
    if (event.key === 'ArrowUp' || isVimUpKey(event.key)) {
        selectResult(document.selectedIndexInSearchResultsArray - 1);
    }

    if (event.key === 'ArrowDown' || isVimDownKey(event.key)) {
        selectResult(document.selectedIndexInSearchResultsArray + 1);
    }

    if (event.key === 'Enter') {
        let link = findLinkByIndex(document.selectedIndexInSearchResultsArray);
        let url = link.href;
        if (event.metaKey) {
            window.open(url, '_blank');
        } else {
            document.location = url;
        }
    }
}

function isVimDownKey(key) {
    let isDown = key === 'j' || key === 'J';
    return isDown && !inputTextBoxHasFocus();
}

function isVimUpKey(key) {
    let isUp = key === 'k' || key === 'K';
    return isUp && !inputTextBoxHasFocus();
}

// j and k should work normally if the user's typing in an input box
function inputTextBoxHasFocus() {
    return document.activeElement.type === 'text';
}

// when the plugin activates, select the first result
selectResult(0);
```
