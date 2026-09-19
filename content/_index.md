---
# Leave the homepage title empty to use the site title
title:
date: 2022-10-24
type: landing

sections:
  - block: about.biography
    id: about
    content:
      title: Biography
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin

  - block: collection
    id: posts
    content:
      title: Posts
      subtitle: ''
      text: ''
      # Choose how many pages you would like to display (0 = all pages)
      count: 5
      filters:
        folders:
          - post
        author: ""
        category: ""
        tag: ""
        exclude_featured: false
        exclude_future: false
        exclude_past: false
        publication_type: ""
      offset: 0
      # Page order: descending (desc) or ascending (asc) date.
      order: desc
    design:
      view: compact
      columns: '2'

  - block: portfolio
    id: projects
    content:
      title: Projects
      filters:
        folders:
          - project
      default_button_index: 0
      buttons:
        - name: All
          tag: '*'
        - name: Other
          tag: Demo
    design:
      columns: '1'
      view: showcase
      flip_alt_rows: false

  # ---------------------------------------------------------------------------
  # PUBLICATIONS  -  switched off for now. The two publications are already
  # written and waiting in content/publication/ - see NOTES-FOR-LATER.md
  # ---------------------------------------------------------------------------
  # - block: collection
  #   id: publications
  #   content:
  #     title: Publications
  #     text: ''
  #     filters:
  #       folders:
  #         - publication
  #   design:
  #     columns: '2'
  #     view: citation

  # ---------------------------------------------------------------------------
  # GALLERY  -  currently switched off because the album has no photos yet.
  #
  # TO TURN IT ON:
  #   1. Put your photos in:  assets/media/albums/gallery/
  #      (.jpg / .jpeg / .png only - no other file types, or the build breaks)
  #   2. Delete the leading "# " from the 8 lines below.
  #   3. Do the same for the Gallery entry in config/_default/menus.yaml
  # ---------------------------------------------------------------------------
  # - block: markdown
  #   id: gallery
  #   content:
  #     title: Gallery
  #     subtitle: ''
  #     text: |-
  #       {{< gallery album="gallery" >}}
  #   design:
  #     columns: '1'

  - block: tag_cloud
    content:
      title: Popular Topics
    design:
      columns: '2'
---
