---
layout: post
title: Call the List API
date: 2017-01-27 00:00:00
description: Tutorial on how to call an API and render a list using React Bootstrap in your React.js app.
code: frontend
comments_id: 52
---

Now that we have our basic homepage set up, let's make the API call to render our list of notes.

### Make the Request

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Add the following below the `constructor` block in `src/containers/Home.js`.

``` javascript
async componentWillMount() {
  if (this.props.userToken === null) {
    return;
  }

  this.setState({ isLoading: true });

  try {
    const results = await this.notes();
    this.setState({ notes: results });
  }
  catch(e) {
    alert(e);
  }

  this.setState({ isLoading: false });
}

notes() {
  return invokeApig({ path: '/notes' }, this.props.userToken);
}
```

<img class="code-marker" src="{{ site.url }}/assets/s.png" />And include our API Gateway Client helper in the header.

``` javascript
import { invokeApig } from '../libs/awsLib';
```

All this does, is make a GET request to `/notes` on `componentWillMount` and puts the results in the `notes` object in the state.

Now let's render the results.

### Render the List

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Replace our `renderNotesList` placeholder method with the following.

``` coffee
renderNotesList(notes) {
  return [{}].concat(notes).map((note, i) => (
    i !== 0
      ? ( <ListGroupItem
            key={note.noteId}
            href={`/notes/${note.noteId}`}
            onClick={this.handleNoteClick}
            header={note.content.trim().split('\n')[0]}>
              { "Created: " + (new Date(note.createdAt)).toLocaleString() }
          </ListGroupItem> )
      : ( <ListGroupItem
            key="new"
            href="/notes/new"
            onClick={this.handleNoteClick}>
              <h4><b>{'\uFF0B'}</b> Create a new note</h4>
          </ListGroupItem> )
  ));
}

handleNoteClick = (event) => {
  event.preventDefault();
  this.props.history.push(event.currentTarget.getAttribute('href'));
}
```

<img class="code-marker" src="{{ site.url }}/assets/s.png" />And include the `ListGroupItem` in the header so that our `react-bootstrap` import looks like so.

``` javascript
import {
  PageHeader,
  ListGroup,
  ListGroupItem,
} from 'react-bootstrap';
```

The code above does a few things.

1. It always renders a **Create a new note** button as the first item in the list (even if the list is empty). We do this by concatenating an array with an empty object with our `notes` array.

2. We render the first line of each note as the `ListGroupItem` header by doing `note.content.trim().split('\n')[0]`.

3. And `onClick` for each of the list items we navigate to their respective pages.

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Let's also add a couple of styles to our `src/containers/Home.css`.

``` css
.Home .notes h4 {
  font-family: "Open Sans", sans-serif;
  font-weight: 600;
  overflow: hidden;
  line-height: 1.5;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.Home .notes p {
  color: #666;
}
```

Now head over to your browser and you should see your list displayed.

![Homepage list loaded screenshot]({{ site.url }}/assets/homepage-list-loaded.png)

And if you click on the links they should take you to their respective pages.

Next up we are going to allow users to view and edit their notes.
