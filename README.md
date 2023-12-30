# Overview

This is my collection of javascript snippets i've built for Logseq. These may either extend functionality or provide new styles for the overall look of the application.

## Removing Tagged References
Tagged References have always been an annoyance for me due to the fact that it was always repetitive to have a reference plus a hard link in the tagged section. So this snippet removes those references from the reference panel:
```js
function updateAndRemoveTaggedReferences() {
    const observer = new MutationObserver((mutationsList) => {
        for (const mutation of mutationsList) {
            if (mutation.type === 'childList' || mutation.type === 'attributes') {
                findAllMatchedElements();
            }
        }
    });

    function findAllMatchedElements() {
        /* This function will iterate through every page + block reference to get a count for how many tags
        vs page references exists for a page reference. If the tags count is equal to the total page reference
        count, then we delete the entire page element, otherwise we only remove the tag reference. */
        let pageNameElement = parent.document.querySelector('.title .title').getAttribute('data-ref')
        let allPageReferences = parent.document.querySelectorAll('.references-blocks-wrap')
        let pageRef = parent.document.querySelectorAll('.references-blocks-wrap>.lazy-visibility')

        // Iterate through each page element
        for (let page of pageRef) {
            let matchedReferences = 0;
            let matchedTagged = 0
            let matchedRefElement = null;
            // Iterate through each block references
            let allReferences = page.querySelectorAll('.references-blocks-wrap .page-ref')
            let currentReferences = Array.from(allReferences).filter(el => el.innerText.trim().toLowerCase().includes(pageNameElement.toLowerCase()));
            for (let currentRef of currentReferences) {
                let currentClassList = currentRef.parentNode.parentNode
                if (currentClassList.classList.contains('page-reference')) {
                    matchedReferences += 1
                }
                else if (currentClassList.classList.contains('page-property-value')) {
                    matchedTagged += 1
                    // set 11 parent levels up
                    let matchedRefElement = currentClassList;
                    // Loop 11 times to move up 11 levels
                    for (let i = 0; i < 11; i++) {
                        if (matchedRefElement.parentNode) {
                            matchedRefElement = matchedRefElement.parentNode;
                        }
                    }
                    removeElement(matchedRefElement)
                }
                if (matchedTagged >= 1 & matchedReferences == 0) {
                    removeElement(page)
                }
                console.log(matchedReferences, matchedTagged)
            }
        }
    }

    function removeElement(element) {
        if (element && element.parentNode) {  // Check if the element exists and has a parent
            element.parentNode.removeChild(element);
        }
    }

    observer.observe(document.body, {
        subtree: true,
        childList: true,
        characterData: true,
    });

    findAllMatchedElements();
}

updateAndRemoveTaggedReferences();
```