**Binary Tree**
-----

1) The depth of a node is the distance to it from the root (which is at depth 0.) Define a function max_depth that returns the maximum depth of all leaves in a binary tree.
```ocaml
type 'a binarytree = 
	| Empty
	| Node of 'a * 'a binarytree * 'a binarytree ;;
```
```ocaml
let max_depth tr = 
	let rec loop t md = 
		match t with
			| Empty -> -1
			| Node(value, Empty, Empty) -> md
			| Node(value, left, right) -> 
				let lmax = loop left (md+1) in 
				let rmax = loop right (md+1) in 
				max lmax rmax in
				(* if lmax >= rmax then lmax else rmax in *)
				
	loop tr 0;;
```
2)  An HTML document can be represented as tree known as the document object model tree.
Each node in the tree represents an HTML element such as < head>, < body>, and < h1>. A node�s children correspond to elements contained within another it. For example, the < body>tag may contain multiple < h1> elements as children.
(a) Define a type domtree, where each node contains
� a tag such as < h1> and optionally some text. The tags < head>, < title>, < body>, < h1> and < p> should be supported.
� zero or more children (the DOM is a multiway tree.)
```ocaml
type htmltag = 
	| Html
	| Head 
	| Title 
	| Body 
	| H1 
	| P ;;

type domtree = 
	| Content of string
	| Node of htmltag * domtree list ;;

```
(b) Define the operation count_tag t which counts the number of occurrences of tag t in the tree.
```ocaml
let rec count_tag (dtr:domtree) (t:htmltag) :int = 

	let rec countlist (l:domtree list) :int = 
		match l with 
			| [] -> 0
			| hd::tl -> (count_tag hd t) + (countlist tl) in
			
	match dtr with
		| Content(s) -> 0
		| Node(v,l) -> 
			if v = t then 1 + (countlist l) 
			else countlist l ;;
```
(c) Define the operation dom_tostring that takes a domtree and returns its string representation.
```ocaml
let string_of_htmltag (tag:htmltag) :string = 
	match tag with
		| Html -> "html"
		| Head -> "head"
		| Title -> "title"
		| Body -> "body"
		| H1 -> "h1"
		| P -> "p" ;;
	
let rec string_of_domtree (dtr:domtree) :string =
	
	let rec string_of_domlist (l:domtree list) :string = 
		match l with 
			| [] -> ""
			| hd::tl -> (string_of_domtree hd) ^ (string_of_domlist tl) in
			
	match dtr with
		| Content(s) -> s
		| Node(v,l) ->
			let stag = string_of_htmltag v in
			"<" ^ stag ^ ">" ^ (string_of_domlist l) ^ "</" ^ stag ^ ">";;
```
Here is sample domtree that you can play :)
```ocaml
let dt = Node(Html,
				[Node(Title, [Content("My Web Page")]); 
				 Node(Body, 
					[Node(H1, [Content("Welcome !!!")]);
					 Node(P, [Content("Hello world")]);
					 Content("This is so coool :) ")
					]);
				]
			) ;;
```
This will output
```ocaml
<html><title>My Web Page</title><body><h1>Welcome !!!</h1><p>Hello world</p>This is so coool :) </body></html>
```

3) An order statistics tree is a BST that supports two additional operations
	i. rank x returns the number of keys that are less than or equal to x.
	ii. select k find the k-th smallest element in the tree.
To implement these operations we keep a count of the number of nodes in each subtree at each node. The count at leaves will be 1 and the count at the root will be the total number of nodes in the tree.
(a) Define a type ostree by adding a count to binary tree nodes.
```ocaml
type 'a ostree = 
	| Empty
	| Node of 'a * int * 'a ostree * 'a ostree ;;
```
(b) Define the operations insert, rank and, select for the ostree type.
```ocaml
let rec insert e t = 
	match t with 
		| Empty -> Node(e, 1, Empty, Empty)
		| Node(v, count, lt, rt) -> 
				if e < v then Node(v, count+1, insert e lt, rt)
				else if e > v then Node(v, count+1, lt, insert e rt)
				else Node(v, count, lt, rt) ;;
```
```ocaml
let rec rank (x:int) (t:'a ostree) :int = 
	match t with 
		| Empty -> failwith "error: empty list."
		| Node(v, count, lt, rt) -> 
				if x<v then rank x lt
				else if x=v then
					if lt != Empty then 
						let Node(lv, lcount, llt, lrt) = lt in 
							lcount + 1
					else 1
				else (*if x>v then *)
					if lt != Empty then 
						let Node(lv, lcount, llt, lrt) = lt in
							lcount + 1 + (rank x rt)
					else (1 + rank x rt);;
```
```ocaml
let rec select (k:int) (t:'a ostree) = 
	match t with
		| Empty -> failwith "error: empty list."
		| Node(v, count, lt, rt) -> 
			let leftcount = 
				if lt = Empty then 0
				else (let Node(lv, lcount, llt, lrt) = lt in lcount) in
				
			if k <= leftcount then (select k lt)
			else if k = leftcount + 1 then Node(v, count, lt, rt)
			else (select (k-leftcount-1) rt) ;;
```
*Note:*
Questions were extracted from CO225 Lab 10 (2014 fall) by Mr. Ziyan Maraikar 

