# Animation Effects in React Apps

## CSS transition으로 애니메이션 효과 주기

- `transition` 속성은 항상 활성화되어 있어서, 단순히 속성을 추가함으로써, 애니메이션 효과를 줄 수 있다.

```css
.challenge-item-details-icon {
  display: inline-block;
  font-size: 0.85rem;
  margin-left: 0.25rem;
  transition: transform 0.3s ease-in;
}

.challenge-item-details.expanded .challenge-item-details-icon {
  transform: rotate(180deg);
}
```

```html
<div
  className={`challenge-item-details ${isExpanded ? 'expanded' : undefined}`}
>
  <p>
    <button onClick={onViewDetails}>
      View Details{' '}
      <span className="challenge-item-details-icon">&#9650;</span>
    </button>
  </p>

  {isExpanded && (
    <div>
      <p className="challenge-item-description">
        {challenge.description}
      </p>
    </div>
  )}
</div>
```
