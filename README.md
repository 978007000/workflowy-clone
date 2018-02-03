# Workflowy Clone Notes
## Basics
Workflowy is a structured text editor built around lists. Lists can have sublists and those sublists in turn can have their own lists. You can “focus” on a list by clicking on it, during which point that list becomes the root list in your view(much like clicking on a folder in a file explorer). The navbar shows  where you are in the list hierarchy.

You can edit lists by clicking on their content and typing. You can add a new list by pressing enter while editing a list item. If you’re at the end of the item you’ll get a new item. If you’re in the middle you’ll split the list in two. If you’re at the start you’ll create a new list above the current list.

You can delete items by either clicking on the “delete” button in the item’s menu. Alternatively if the item is empty and you hit backspace, that will also delete the item.


## Data
```
{
  nodes: {
    [uuid]: { content: [node's content] }
  }
  nodeOnNodes: {
    [uuid]: [array of node ids]
  }
  root: [id of the root node, where rendering starts]
  focus: {
    id: [id of currently focused node],
    cursorPosition: [integer representing where in the node's content we are at]
  }
}
```

## Tasks
- [x] normalize data
- [x] Use containers
- [x] get rendering working
- [x] handle new nodes
	- [x] support splitting existing nodes by hitting return in the middle of their content
	- [x] support adding new nodes by pressing enter at the end of the content of an existing node
	- [x] when the current node has no children, the new node should be a child of the current node’s parent
- [x] Editing node content
	- [x] Restore cursor position when components are re-rendered
		- [x] keep track of cursor position at all times
	- [x] Allow insertion of whitespace correctly at end of node
- [x] Move items left and right with `tab` and `shift` `+` `tab`
	- [x] move right
	- [x] move left
		- [x] Figure out a good way to make grandparent discovery easy
		options:
			* child -> parent index
			* add parent attribute to each node in the state
				* I’m gonna go with this for now
	- [x] shifting indentation when focused should limit movement to the current scope
	FIX: check if parent is root
- [ ] Fix backspace on selections
	- [ ] Use onInput
- [x] Backspace on an empty item with no children should delete that entry
	* focus changes to the item above it, with the cursor at the end
	* if there is no item above it, then change focus to the one below it, with the cursor at the start
	* if there are no items above or below, then we can’t delete
	* we also can’t delete when self is root
- [x] Update the focused element in the state when an item is clicked on or a key is changed
- [x] Move around with the up and down keys
- [ ] Allow hiding and unhiding
- [ ] undo/redo
- [x] add bullet icon next to note
- [ ] dates
## Bugs
* For some reason new items have invisible `\n`s inside of them. 

## Notes
### Moving up:
To move up in the list hierarchy, you want the cursor focus to change to the node visually above the current node.
```
IF the current node is the first child of its parent
THEN move the focus to the parent node

ELSE 
SET CurrentRelative = the immediately preceding sibling of the current node
UNTIL CurrentRelative has no children {
  currentRelative = currentRelative.children.last
}
Change Focus to current relative
``` 
* alternatively we can create a data structure that keeps track of node order across parent/child boundaries and update it when the state changes
### Moving Down:
To move down in the list hierarchy, you want the cursor focus to change to the node visually below the current node
```

IF the current node has any children, then move focus to the first child

IF the current node is the last child, then see if the current node's parent has any children which are not the last child amongst their siblings. if such a child exists, change the focus. Otherwise change the current node to the current node's parent and repeat.
```