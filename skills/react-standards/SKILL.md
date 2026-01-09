---
name: react-human-made
description: Human Made React component standards. Apply when writing React components, reviewing React code, or building WordPress block editor interfaces. Covers functional components, hooks, PropTypes, and component organization.
---

# Human Made React Standards

## Component Style

- Use functional components with hooks for most cases
- Class components only when required by external libraries
- Keep components focused and single-purpose
- Extract complex logic into custom hooks

## Semantic HTML

- Use semantic HTML5 elements (`<article>`, `<section>`, `<nav>`, etc.)
- `onClick` handlers only on focusable elements (buttons, links)
- Ensure keyboard accessibility for interactive elements
- Use ARIA attributes when semantic HTML is insufficient

## Props and State

- Specify PropTypes for all component properties
- Prefer props over state; lift state up when needed
- Avoid storing state in DOM
- Use controlled components for form inputs

## Component Organization

- Co-locate styles and tests with components
- One component per file for significant components
- Small helper components can share a file with their parent

## Example Component

```jsx
import PropTypes from 'prop-types';
import { useState, useCallback } from 'react';

const UserCard = ( { user, onSelect } ) => {
    const [ isExpanded, setIsExpanded ] = useState( false );

    const handleToggle = useCallback( () => {
        setIsExpanded( prev => ! prev );
    }, [] );

    return (
        <article className="user-card">
            <h3>{ user.name }</h3>
            <button onClick={ handleToggle }>
                { isExpanded ? 'Collapse' : 'Expand' }
            </button>
            { isExpanded && (
                <div className="user-card__details">
                    <p>{ user.email }</p>
                    <button onClick={ () => onSelect( user.id ) }>
                        Select User
                    </button>
                </div>
            ) }
        </article>
    );
};

UserCard.propTypes = {
    user: PropTypes.shape( {
        id: PropTypes.number.isRequired,
        name: PropTypes.string.isRequired,
        email: PropTypes.string.isRequired,
    } ).isRequired,
    onSelect: PropTypes.func.isRequired,
};

export default UserCard;
```

## WordPress Block Editor

When building Gutenberg blocks:
- Use `@wordpress/element` for React (it re-exports React)
- Use `@wordpress/components` for UI primitives
- Use `@wordpress/block-editor` for block-specific components
- Follow the block.json schema for block registration
- Use `@wordpress/data` for state management with WordPress stores
