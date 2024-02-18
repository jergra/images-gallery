Feb 16, 2024

dev/images-gallery-next14

From tutorial (with a few small changes):
  Build Image gallery with Next.js and Supabase Storage
  https://www.youtube.com/watch?v=YmI8INix-d0

  by Daily Web Coding

Multi=user images gallery website built with Next14, supabase, supabase authentication, supabase storage, uppy.

Deployed:

- Once deployed need to change homepage url from localhost  to deployed url
at github for OAuth purposes - personal settings - developer settings - OAuth Apps.
Also at Supabase, under Authentication - URL Configuration, change Site URL from 
localhost to deployed URL. 


todo: 

push to github
deploy



.env.local:

NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=


## Profile table

```sql
create table
  public.profiles (
    id uuid not null,
    created_at timestamp with time zone not null default now(),
    email text not null,
    display_name text null,
    image_url text null,
    constraint profiles_pkey primary key (id),
    constraint profiles_id_fkey foreign key (id) references auth.users (id) on update cascade on delete cascade
  ) tablespace pg_default;
```

## Auth Trigger Function

```sql
begin

  insert into public.profiles(id,email,display_name,image_url)
  values(
    new.id,
    new.raw_user_meta_data ->> 'email',
    COALESCE(new.raw_user_meta_data ->> 'user_name',new.raw_user_meta_data ->> 'name'),
    new.raw_user_meta_data ->> 'avatar_url'
  );
  return new;

end;
```

### Auth Trigger Creation

```sql
create trigger create_user_on_signup
after insert on auth.users for each row
execute function create_user_on_signup ();
```

### Remove Trigger

```sql
drop trigger create_user_on_signup on auth.users;
```
