# perMonad
An indexed monad used to track permissions on aggregated data on the type level.

# Idea
Use the `ixmonad` and `type-level-sets` libraries to build a permission monad.

The idea is, to use a type-level set of type-level permissions to represent the permissions needed to get a value:
```Haskell
type PersonId = Int
type PersonName = String
mapPersonName :: (PersonName -> PersonName) 
              -> PersonId 
              -> Perm ('Set '[ 'Read 'PersonName, 'Write 'PersonName]) PersonName
```

The `PersonName` after this map can only be extracted by code that has proof they have the neccessary permissions.

Importantly, this is implemented using an indexed monad, such that the programmer does not need to handle type level permissions themselves:

```Haskell

readFirstname :: PersonId -> Perm ('Set '[ 'Read 'PersonFirstname ]) String
readLastname :: PersonId -> Perm ('Set '[ 'Read 'PersonLastname ]) String

readName :: PersonId -> Perm ('Set '[ 'Read 'PersonFirstname, 'Read 'PersonLastname ]) String
readName pid = do
    firstname <- readFirstname pid
    lastname <- readLastname pid
    return $ firstname ++ " " ++ lastname
```

Sadly, this also means that types are getting rather complicated; so most programmers are going to not write type signatures themselves.
