# jet-query
Simple query/caching tool for browser-based JavaScript/TypeScript.

```ts
import jq from ' jet-query';
import axiosLib from 'axios';

const axios = axiosLib({  withCredentials: true,}));

function fetchOne(id: number): Promise<[IUser, AxiosError]> {
  const path = `/api/users/${id}`;
  return jq.fetch<IUser, AxiosError>({
    key: path,
    queryFn() {
      const resp = await axios.get(path);
      return resp.data;
    },
    expire: 5000, // cache expires then, setTimeout will delete it
    persist: false, // cache stored in localStorage
    refresh: true, // refetch every time
  });
}

// Can also Promise<[Something, error]>
function addOne(user: IUser): Promise<[error]> {
  const key =  '/api/users';
  return jq.mutate<AxiosError>({
    path: key,
    mutateFn: () => await axios.post(key, { user }),
  })
}

// In zustand
const useUserStore = create(set => ({
  users: null,
  async fetchUser(id: number) {
    const [ user, error ] = await API.fetchUsers(id); 
     set({ user });
  },
}));
```
