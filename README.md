This is the code for the project.
The custom action code for calling the rpc procedure in Supabase

```
import 'package:supabase_flutter/supabase_flutter.dart';

Future<List<dynamic>> searchProjects(String search) async {
  try {
    final supabase = SupaFlow.client;
    final response = await supabase.rpc('search_projects', params: {
      'p_search_term': search,
    });
    // Print the raw response for debugging purposes
    print('Raw Response: $response');

    // Convert the response to a list of maps

    return response;
  } catch (e) {
    print('Exception: $e');
    // Return an empty list in case of an exception
    return [];
  }
}
```

The Postgres query to  seach the column for like terms. No case sensitive

```
CREATE OR REPLACE FUNCTION search_projects(p_search_term text)
RETURNS JSON
AS $$
DECLARE
    result_json JSON;
BEGIN
    SELECT COALESCE(json_agg(row_to_json(t)), '[]')::json
    INTO result_json
    FROM (
        SELECT project_nr, project_nm, client, value, manager
        FROM projects
        WHERE LOWER(projects.project_nm) LIKE LOWER('%' || p_search_term || '%')
    ) t;

    RETURN result_json;
END;
$$ LANGUAGE plpgsql;
```
