learning react hooks by writing react hooks
===========================================

----------------------------------

*by react hook, or by react crook*

----------------------------------

A React component does not know its own name, which can make things tricky. Sometimes, in a JSX expression, you will want to assign a unique identifier:

.. code:: tsx

  const Password: React.FC<{}> = () => (
    <p>
      <label htmlFor={???}>password, please:</label>
      <input id={???} type="password" />
    </p>
  )
  
(Note the awkward ``htmlFor``. It becomes a ``for=`` attribute, in case you have not seen it before, but it conflicts with JavaScript's ``for`` keyword. Why do we use the word "for," when we mean "loop"? Why do people like programming?)

Something like this:

.. code:: tsx

  const Password: React.FC<{}> = () => (
    <p>
      <label htmlFor="password">password, please:</label>
      <input id="password" type="password" />
    </p>
  )
  
only works if the React component is only ever instantiated once in the DOM tree, which seems like a difficult thing to guarantee. Maybe there are other people working on the codebase. People with have deadlines and standup Zooms. Maybe they don't have time to read all your Slack messages. Maybe they don't have time for you and your bullshit.

So you turn to your old friend, the UUID:

.. code:: tsx

  import {v4 as uuid} from 'uuid';

  const Password: React.FC<{}> = () => {
    const id = uuid();
    return (
      <p>
        <label htmlFor={id}>password, please:</label>
        <input id={id} type="password" />
      </p>
    );
  }
  
But this seems awfully wasteful. Generating a 128-bit random number and formatting into a string every time the password component re-renders? Seems *awfully* wasteful, like sending someone a photograph as a PNG.

Let's write a hook:

.. code:: tsx

  // in src/hooks.tsx
  import {useState} from 'react';
  import {v4 as uuid} from 'uuid';
  export const useID = (descriptivePrefix = ''): string => {
    const [id] = useState<string>(descriptivePrefix + uuid().slice(0, 8));
    return id;
  }
  
  // in src/password.tsx
  import {useID} from './hooks';
  const Password: React.FC<{}> = () => {
    const id = useID('password');
    return (
      <p>
        <label htmlFor={id}>password, please:</label>
        <input id={id} type="password" />
      </p>
    );
  }
  
The best of both worlds: a debuggable ``for=`` attribute, and we only needed to generate the string once. We never violated the Rules of Hooks, and there is spirit in our cheeks, as we emerge from our long coding session. We were in the flow. *We were flowing.*
