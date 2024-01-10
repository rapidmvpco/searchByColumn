This is the code for the project.
The custom action code for calling the rpc procedure in Supabase

```
import 'package:supabase_flutter/supabase_flutter.dart';

Future<List<dynamic>> searchTable(String search) async {
  try {
    final supabase = SupaFlow.client;
    final response = await supabase.rpc('search_table', params: {
      'p_search_term': search,
    });
    // Print the raw response for debugging purposes
    print('Raw Response: $response');

   

    return response;
  } catch (e) {
    print('Exception: $e');
    // Return an empty list in case of an exception
    return [];
  }
}
```

The Postgres query to  seach a specific column for like terms. No case sensitive

```
CREATE OR REPLACE FUNCTION search_table_column(p_search_term text)
RETURNS JSON
AS $$
DECLARE
    result_json JSON;
BEGIN
    SELECT COALESCE(json_agg(row_to_json(t)), '[]')::json
    INTO result_json
    FROM (
        SELECT SELECT your_column_1, your_column_2, .....
        FROM your_table
        WHERE LOWER(your_table.your_column_1) LIKE LOWER('%' || p_search_term || '%')
    ) t;

    RETURN result_json;
END;
$$ LANGUAGE plpgsql;
```

This query searches the whole table for the keyword and returns like results

```
CREATE OR REPLACE FUNCTION search_table(p_search_term text)
RETURNS JSON
AS $$
DECLARE
    result_json JSON;
BEGIN
    SELECT COALESCE(json_agg(row_to_json(t)), '[]')::json
    INTO result_json
    FROM (
        SELECT your_column_1, your_column_2, .....
        FROM your_table
        WHERE
            to_jsonb(your_table)::text ILIKE '%' || p_search_term || '%'
    ) t;

    RETURN result_json;
END;
$$ LANGUAGE plpgsql;
```
