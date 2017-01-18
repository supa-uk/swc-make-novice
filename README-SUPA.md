Notes on the SUPA branch
========================

Building
--------

You need to install [Jekyll](https://jekyllrb.com).  Then `make site`.

Appearance
----------

The CSS on the style comes from ‘Bootstrap’ which is a clearly very
fancy javascript+CSS framework.  After a certain amount of flailing
around, it appears that the best way to manage this is to go to
[their website](http://getbootstrap.com/customize/)
and customise a set of layouts.  I chose to turn off all Javascript
and jQuery components, and choose

  * brand-primary: darken(#043469, 6.5%) (same as the SUPA logo colour)

  * brand-success: lighten(#043469, 50%)

  * brand-info: #5cfe4f

  * brand-warning: #fee41e

  * brand-danger: #fe1725

  * Under ‘Typography’: font-family-sans-serif is "Gill Sans",
    "Helvetica Neue", Helvetica, Arial, sans-serif (though this is
    overridden in `swc.css`), and headings-font-weight: 300 (from
    500), and font size multipliers (h1: 2.6 -> 2.9, h2: 2.15 -> 2.3,
    h3: 1.7->1.9, h4: 1.25 -> 1.37); ranged right in `swc.css`

  * Under ‘Form states and alerts’:

    - @state-success-text darken(@brand-success, 50%)
    - @state-success-bg lighten(@brand-success, 50%)
    - @state-success-border darken(spin(@state-success-bg, -10), 5%)
    - @state-info-text darken(@brand-info, 40%)
    - @state-info-bg lighten(@brand-info, 10%)
    - @state-info-border darken(spin(@state-info-bg, -10), 7%)
    - @state-warning-text darken(@brand-warning, 30%)
    - @state-warning-bg lighten(@brand-warning, 20%)
    - @state-warning-border darken(spin(@state-warning-bg, -10), 5%)
    - @state-danger-text darken(@brand-danger, 30%)
    - @state-danger-bg lighten(@brand-danger, 20%)
    - @state-danger-border darken(spin(@state-danger-bg, -10), 5%)
