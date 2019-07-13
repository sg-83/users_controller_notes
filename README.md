# Users Controller Notes
  - Use more descriptive names for methods, or at least include a comment. `octokit` as a method name holds no meaning for someone looking at the code without prior contextual knowledge. Maybe unlikely, but you may want to switch to a different github api wrapper in the future, so you'd want to name it something agnostic of the particular gem. Maybe call this `github_api`?

  - `#show`: Every show request is making 2 calls to the github API, introducing a lot of latency. Adding caching here could greatly speed up this action.

  - Line 3: Would be better to just assign 'github_user' as an instance variable, rather than declaring variables from each method on the object.

  - Line 14: Personal preference, but should use a do/end multiline block for better readability.

  - Line 16: Assumes presence of current_user. Safer to check for presence of current_user before calling further methods.

  - Line 16: Seems to contain an error where `=` was used instead of `==`. From the naming of the variables I think the intent is to check if the currently logged in user is the same as the current user show page. The actual code is instead setting the currently logged in users' github username to the username returned by the github api, and then setting the @user_is_current variable equal to that as well. I think the corrected version of this line is: `@user_is_current = (current_user.github_username == @nickname)`.

  - Line 34: 52 string concatenations can be slow, and we are doing this every time, regardless of a cache hit. It would be better to move this code inside the fetch block, so the concatenation only happens upon a cache miss.

  - Line 40: We're storing a boolean in the `@prs_incomplete` variable. A better name would be `@has_incomplete_prs`.

  - Line 47: Dont need trailing comma.

  - `#query_prs refactor`: We can return a hash from the cache fetch block that contains all the data that we later put into instance variables. This saves us having to do a cache fetch plus computations every time. We only need to compute totals and do grouping on a cache miss.

  - Line 54: Can be refactored to: `days_until_next_saturday = (next_saturday - Date.today).to_i.days`, and line 55 can be removed.

  - Line 57: We can again move the code for generating the collection from the API request within the cache block, otherwise we are creating the collection from cached data on each call.

  - Line 62: We may want to create a Helper for generating 'Github Event' hashes and centralize all title generation logic there, rather than keeping it in the controller. This would ensure easy reuse of the same logic elsewhere in the app, and maintain consistency.

  - Line 70: We are assigning the `date` and `url` variables a value as a side effect in a case statement that should only be assigning the `title`. We should refactor to explicitly assign the `date` and `url` variables in different logic trees. The above advice for creating a Helper for generating these Github Event hashes would help with this.

  - Line 76: should be moved earlier, preferably inserted after line 72 and changed to: `next if payload.issue.user.login == @nickname`. We don't want to do extra work if there's just going to be an early exit later.

  - Line 82:  I would remove the '.html_safe' from this: `"pushed <code>#{branch}</code>".html_safe` and instead call that in the view, making it easier to see where we are allowing potential code injection.

  - Line 98 and 99 could be moved to a method and called in the title generation logic so we can early exit as soon as possible.

  - Line 105: Don't need trailing comma.
