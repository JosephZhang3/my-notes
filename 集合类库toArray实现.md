# Collection2Array


#### 我们来仔细研究下很多集合类都继承了的抽象类AbstractCollection中的方法toArray(T[] a)，注意是带参数（泛型数组）的！

    public <T> T[] toArray(T[] a)

    Returns an array containing all of the elements in this collection; the runtime type of the returned array is that of the specified array. If the collection fits 
    in the specified array, it is returned therein. Otherwise, a new array is allocated with the runtime type of the specified array and the size of this collection.

    If this collection fits in the specified array with room to spare (i.e., the array has more elements than this collection), the element in the array immediately
    following the end of the collection is set to null. (This is useful in determining the length of this collection only if the caller knows that this collection does 
    not contain any null elements.)

    If this collection makes any guarantees as to what order its elements are returned by its iterator, this method must return the elements in the same order.

    Like the Collection.toArray() method, this method acts as bridge between array-based and collection-based APIs. Further, this method allows precise 
    control over the runtime type of the output array, and may, under certain circumstances, be used to save allocation costs.

    Suppose x is a collection known to contain only strings. The following code can be used to dump the collection into a newly allocated array of String:

         String[] y = x.toArray(new String[0]);

    Note that toArray(new Object[0]) is identical in function to toArray().

    This implementation returns an array containing all the elements returned by this collection's iterator in the same order, stored in consecutive elements of 
    the array, starting with index 0. If the number of elements returned by the iterator is too large to fit into the specified array, then the elements are returned 
    in a newly allocated array with length equal to the number of elements returned by the iterator, even if the size of this collection changes during iteration, 
    as might happen if the collection permits concurrent modification during iteration. The size method is called only as an optimization hint; the correct result 
    is returned even if the iterator returns a different number of elements.

    This method is equivalent to:


     List<E> list = new ArrayList<E>(size());
     for (E e : this)
         list.add(e);
     return list.toArray(a);


    Specified by:
        toArray in interface Collection<E>
    Type Parameters:
        T - the runtime type of the array to contain the collection
    Parameters:
        a - the array into which the elements of this collection are to be stored, if it is big enough; otherwise, a new array of the same runtime type is allocated 
        for this purpose.
    Returns:
        an array containing all of the elements in this collection
    Throws:
        ArrayStoreException - if the runtime type of the specified array is not a supertype of the runtime type of every element in this collection
        NullPointerException - if the specified array is null 

-

    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        // Estimate size of array; be prepared to see more or fewer elements
        int size = size();
        T[] r = a.length >= size ? a :
                  (T[])java.lang.reflect.Array
                  .newInstance(a.getClass().getComponentType(), size);
        Iterator<E> it = iterator();

        for (int i = 0; i < r.length; i++) {
            if (! it.hasNext()) { // fewer elements than expected
                if (a == r) {
                    r[i] = null; // null-terminate
                } else if (a.length < i) {
                    return Arrays.copyOf(r, i);
                } else {
                    System.arraycopy(r, 0, a, 0, i);
                    if (a.length > i) {
                        a[i] = null;
                    }
                }
                return a;
            }
            r[i] = (T)it.next();
        }
        // more elements than expected
        return it.hasNext() ? finishToArray(r, it) : r;
    }
    
上面是toArray(T[] a)方法的注释和实现代码
