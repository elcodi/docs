Comment Reference
=================

``` yaml
elcodi_comment:
    mapping:
        comment:
            # Comment entity implementing CommentInterface
            class: Elcodi\Component\Comment\Entity\Comment
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCommentBundle/Resources/config/doctrine/Comment.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        comment_vote:
            # Vote entity implementing VoteInterface
            class: Elcodi\Component\Comment\Entity\Vote
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCommentBundle/Resources/config/doctrine/Vote.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true

    comments:
        # Your comments will be cached, so maybe you want to customize the key
        cache_key: comments
        # A comment can be parsed. Check the possible parsers and chose yours
        # Take in account that this value must be a service name
        parser: elcodi.dummy_parser_adapter
```
